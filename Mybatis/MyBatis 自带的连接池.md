---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 自带连接池详解

## 基本认识

- MyBatis 自带连接池：`PooledDataSource`
- 特点：简单的池化实现，方便但不如 Druid、HikariCP 强大
- 核心参数
  - `poolMaximumActiveConnections`：最大活跃连接数
  - `poolMaximumIdleConnections`：最大空闲连接数
  - `poolMaximumCheckoutTime`：最大检查时间
  - `poolTimeToWait`：等待时间
  - `poolPingQuery`：检测连接是否可用的 SQL

---

## 连接池的创建与分析

- 类型
  - `UnpooledDataSource`：无连接池，每次创建新连接
  - `PooledDataSource`：池化实现，使用对象池管理连接

### 获取连接过程

```java
private Connection doGetConnection(Properties properties) throws SQLException {
    initializeDriver();
    Connection connection = DriverManager.getConnection(url, properties);
    configureConnection(connection);
    return connection;
}
```

### 包装后的代理连接

```java
@Override
public Connection getConnection() throws SQLException {
    return popConnection(dataSource.getUsername(), dataSource.getPassword()).getProxyConnection();
}
```

---

## 连接池内部结构

### PoolState

```java
public class PoolState {
    protected final List<PooledConnection> idleConnections = new ArrayList<>();
    protected final List<PooledConnection> activeConnections = new ArrayList<>();
    protected long requestCount = 0;
    protected long accumulatedRequestTime = 0;
    protected long accumulatedCheckoutTime = 0;
    protected long accumulatedWaitTime = 0;
    protected long hadToWaitCount = 0;
    protected long badConnectionCount = 0;
}
```

### PooledConnection

```java
class PooledConnection implements InvocationHandler {
    private final int hashCode;
    private final PooledDataSource dataSource;
    private final Connection realConnection;
    private final Connection proxyConnection;
    private long checkoutTimestamp;
    private long createdTimestamp;
    private long lastUsedTimestamp;
    private boolean valid;

    public PooledConnection(Connection connection, PooledDataSource dataSource) {
        this.hashCode = connection.hashCode();
        this.realConnection = connection;
        this.dataSource = dataSource;
        this.createdTimestamp = System.currentTimeMillis();
        this.lastUsedTimestamp = System.currentTimeMillis();
        this.valid = true;
        this.proxyConnection = (Connection) Proxy.newProxyInstance(
            Connection.class.getClassLoader(),
            new Class<?>[] { Connection.class },
            this
        );
    }
}
```

### Connection close 方法被代理

```java
@Override
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    String methodName = method.getName();
    if (CLOSE.equals(methodName)) {
        dataSource.pushConnection(this);
        return null;
    }
    try {
        return method.invoke(realConnection, args);
    } catch (Throwable t) {
        throw ExceptionUtil.unwrapThrowable(t);
    }
}
```

---

## popConnection 流程

1. 调用 `PoolState`，判断空闲连接池是否有连接
2. 如果有空闲连接：直接取出并返回
3. 如果没有空闲连接但活跃连接未超最大值：创建新连接，包装成 `PooledConnection`
4. 如果活跃连接已达最大值：

   - 检查最老的活跃连接是否超时
   - 超时则回收并复用
   - 未超时则进入等待队列

5. 检测连接是否有效，无效则丢弃并重试

---

## pushConnection 流程

- 当调用 `close()` 方法时，不是真正关闭连接，而是将连接归还到连接池
- 更新连接状态：`activeConnections → idleConnections`
- 若池中空闲连接已满，丢弃该连接

---

## 面试速记

- **MyBatis 自带连接池特点**

  - 简单、轻量，方便快速使用
  - 核心功能：连接复用、空闲/活跃连接管理
  - 不支持高级功能，如连接泄漏检测、SQL 监控、异步回收等

- **适用场景**

  - 小型项目、测试环境、Demo
  - 不适合高并发、大流量生产环境

- **关键参数面试点**

  - `poolMaximumActiveConnections`：最大活跃连接数，决定系统同时处理请求的上限
  - `poolMaximumIdleConnections`：最大空闲连接数，避免频繁创建/销毁连接
  - `poolMaximumCheckoutTime`：连接最大使用时间，防止“长时间占用连接”
  - `poolTimeToWait`：获取连接时等待时间，避免线程无限阻塞
  - `poolPingQuery`：检测连接是否可用的 SQL，用于防止获取到死连接

- **连接池工作流程面试点**

  1. **获取连接**：先从空闲池取 → 活跃未满则创建 → 活跃已满则等待或回收超时连接
  2. **归还连接**：调用 `close()` → 代理方法拦截 → 移动到空闲池或丢弃
  3. **连接检测**：空闲/活跃连接都可能被验证是否可用

- **面试常见问题**

  - **为什么生产不推荐 MyBatis 自带池？**
    简单、不稳定、不支持高级监控和性能优化，容易出现连接泄漏或线程阻塞
  - **如何解决连接泄漏？**
    使用更成熟的连接池（HikariCP、Druid），并设置合理的 `maxLifetime` 和 `checkoutTimeout`
  - **连接池和 ORM 框架的关系？**
    MyBatis 提供 ORM 映射，连接池负责管理数据库连接，两者职责分离

- **推荐替代方案**

  - **Druid**：阿里开源，功能全面，支持 SQL 监控、慢查询、统计分析
  - **HikariCP**：高性能连接池，轻量、低延迟
  - **C3P0**：老牌连接池，稳定，但性能一般

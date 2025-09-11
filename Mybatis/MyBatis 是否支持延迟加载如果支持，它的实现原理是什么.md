---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 延迟加载（Lazy Loading）

## 1. 概述

- **是否支持？** 支持，MyBatis 提供了延迟加载（懒加载）机制。
- **定义：** 只在需要时才加载关联数据，而不是在查询主对象时立即加载。
- **优点：**
  - 提升性能，减少无用数据查询。
  - 特别适合关联关系复杂、大数据量场景。
- **缺点：**
  - 首次访问时可能增加延迟。
  - 过度使用可能导致 N+1 查询问题。

## 2. 实现原理

- 核心机制：**动态代理 + 结果加载器**
- **1. 创建代理对象**
  - 查询主对象时，不立即加载关联对象，而是为关联属性创建**代理对象**
  - 使用 `ProxyFactory` 生成代理（默认 `JavassistProxyFactory` 或 `CglibProxyFactory`）
  - 示例：
    ```java
    if (configuration.isLazyLoadingEnabled()) {
        Object proxy = configuration.getProxyFactory().createProxy(target, loader);
        return proxy;
    }
    ```
- **2. 封装加载器**
  - MyBatis 用 `ResultLoader` 封装延迟加载 SQL 及其参数
  - 示例：
    ```java
    public Object loadResult() throws SQLException {
        return executor.query(ms, parameterObject, rowBounds, resultHandler);
    }
    ```
- **3. 触发延迟加载**
  - 调用代理对象方法时（如 `user.getOrders()`），代理拦截调用，触发 `ResultLoader` 执行 SQL
  - 查询结果会缓存，再次访问时不会重复查询

## 3. 配置方式

- **全局配置（mybatis-config.xml）**
  ```xml
  <settings>
      <!-- 开启延迟加载 -->
      <setting name="lazyLoadingEnabled" value="true"/>
      <!-- 是否激进加载：false 表示只加载被调用的属性 -->
      <setting name="aggressiveLazyLoading" value="false"/>
      <!-- 建议开启，便于参数映射 -->
      <setting name="useActualParamName" value="true"/>
  </settings>
  ```

* `lazyLoadingEnabled`：是否启用懒加载

* `aggressiveLazyLoading`

  - `true`：访问任意属性都会加载所有延迟属性
  - `false`：访问哪个属性就只加载哪个属性（推荐）

* **映射文件配置（示例：一对多）**

  - `User` 与 `Order` 一对多关系
  - 在 `<association>` 或 `<collection>` 中配置 `fetchType="lazy"`

  ```xml
  <resultMap id="userResultMap" type="User">
      <id property="id" column="user_id"/>
      <result property="username" column="username"/>
      <collection property="orders" ofType="Order"
                  select="selectOrdersByUserId"
                  column="user_id"
                  fetchType="lazy"/>
  </resultMap>

  <select id="selectUser" resultMap="userResultMap">
      SELECT id AS user_id, username
      FROM User
      WHERE id = #{userId}
  </select>

  <select id="selectOrdersByUserId" resultType="Order">
      SELECT id, order_date, amount
      FROM Order
      WHERE user_id = #{userId}
  </select>
  ```

## 4. 应用场景

- **一对一关联**

  - `User` 包含 `Address`
  - 只有调用 `user.getAddress()` 时才查询 `Address`

- **一对多关联**

  - `Order` 包含 `OrderItem` 集合
  - 只有调用 `order.getOrderItems()` 时才加载

## 5. 面试速记

- **Q1: 什么是延迟加载？**

  - 定义：按需加载数据，不在主查询时立即加载
  - 优点：节省资源、优化性能
  - 缺点：首次访问有额外延迟，可能带来 N+1 查询问题

- **Q2: 实现原理？**

  - 查询主对象时生成代理对象
  - 代理对象内部持有 `ResultLoader`，保存 SQL 和参数
  - 调用关联属性时，代理拦截方法 → 触发 SQL 执行 → 返回结果并缓存

- **Q3: 如何配置？**

  - **全局：**

    - `lazyLoadingEnabled=true`
    - `aggressiveLazyLoading=false`

  - **映射：**

    - 在 `<association>` / `<collection>` 中加 `fetchType="lazy"`

## 6. 总结口诀

- **代理对象拦截 → 封装 SQL Loader → 按需触发查询**

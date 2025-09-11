# MyBatis 都有哪些 Executor 执行器

## MyBatis 中的三种 Executor 执行器

### 1. SimpleExecutor

- **执行方式**：每次执行 SQL 时，都会创建一个新的 `Statement` 对象。
- **特点**：执行完成后，会立即关闭 `Statement` 对象，不进行缓存。

### 2. ReuseExecutor

- **执行方式**：会将 SQL 语句对象进行缓存。
- **特点**：
  - 根据 SQL 语句对 `Statement` 进行缓存，键为 SQL 语句，值为 `Statement` 对象。
  - 执行相同 SQL 时，会从缓存中获取 `Statement` 对象进行重用，不会重复创建。
  - 该执行器只重用 `Statement`，不缓存结果集。

### 3. BatchExecutor

- **执行方式**：用于批量更新操作，将多条 SQL 语句组合成一个批次。
- **特点**：
  - 仅支持批量更新（`UPDATE`、`DELETE`、`INSERT`）操作。
  - 执行时，会为每个 SQL 语句创建一个 `Statement`，并将其添加到批处理队列中。
  - 当执行 `flushStatements()` 方法时，才会将所有批处理 SQL 提交到数据库。

---

## 执行器使用场景

- **SimpleExecutor**：适用于大多数场景，执行效率高，代码逻辑简单。
- **ReuseExecutor**：适用于需要重复执行相同 SQL 的场景，可以减少 `Statement` 对象的创建和销毁，从而提高效率。
- **BatchExecutor**：适用于批量处理大量更新操作，可以显著减少数据库交互次数，提高整体性能。

---

## Executor 的区别总结

| 特性                | SimpleExecutor | ReuseExecutor               | BatchExecutor      |
| :------------------ | :------------- | :-------------------------- | :----------------- |
| **Statement 句柄**  | 每次都创建     | 缓存后重用                  | 批量使用，批量提交 |
| **操作类型**        | 单条执行       | 单条执行                    | 批量执行           |
| **缓存机制**        | 无             | 对 `Statement` 句柄进行缓存 | 无（批量操作）     |
| **资源释放**        | 立即释放       | 可重用后关闭                | 批量执行后统一释放 |
| **事务管理**        | 每次都开启     | 开启一次，多次执行          | 开启一次，批量提交 |
| **是否支持 SELECT** | 支持           | 支持                        | 不支持             |

---

## 默认执行器

MyBatis 默认使用的执行器是 **SimpleExecutor**。可以通过以下方式设置：

```java
// 在 MyBatis 配置中设置
SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(new Configuration(environment));
SqlSession session = sessionFactory.openSession(ExecutorType.SIMPLE);

// 或者在创建 session 时指定
SqlSession session = sqlSessionFactory.openSession(ExecutorType.SIMPLE);

// 默认执行器类型
public class DefaultSqlSessionFactory implements SqlSessionFactory {
    // ...
    public SqlSession openSession() {
        return openSessionFromDataSource(configuration.getDefaultExecutorType(), null, false);
    }
    // ...
}
```

```java
public class Configuration {
    // ...
    public ExecutorType getDefaultExecutorType() {
        return defaultExecutorType;
    }
    // ...
}
```

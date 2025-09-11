---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 如何实现数据库类型和 Java 类型的转换？

## 核心机制

- 依赖 MyBatis 的 **TypeHandler** 机制
- 功能：
  - 将 Java 类型转换为 JDBC 类型（写入数据库时）
  - 将 JDBC 类型转换为 Java 类型（从数据库读取时）
- 使用场景：
  - MyBatis 在设置参数时（setParameter），通过 JDBCType 和 Java 类型确定 TypeHandler
  - 执行 SQL 后，ResultSetHandler 通过 TypeHandler 将 JDBC 类型转换为 Java 对象

---

## MyBatis 内置的 TypeHandler

- 示例：

  - `IntegerTypeHandler`：处理 `Integer` 和 JDBC 的 `INTEGER`
  - `StringTypeHandler`：处理 `String` 和 JDBC 的 `VARCHAR`
  - `DateTypeHandler`：处理 `java.util.Date` 和 JDBC 的 `TIMESTAMP`

- 注册与映射：
  ```java
  public final class TypeHandlerRegistry {
      private final Map<JdbcType, TypeHandler<?>> jdbcTypeHandlerMap = new EnumMap<>(JdbcType.class);
      private final Map<Type, Map<JdbcType, TypeHandler<?>>> typeHandlerMap = new ConcurrentHashMap<>();
      private final Map<Class<?>, TypeHandler<?>> allTypeHandlersMap = new HashMap<>();
  }
  ```

---

## 自定义 TypeHandler 示例

### 1. 继承 BaseTypeHandler

```java
public class DateTypeHandler extends BaseTypeHandler<Date> {
    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, Date parameter, JdbcType jdbcType)
        throws SQLException {
        ps.setTimestamp(i, new Timestamp(parameter.getTime())); // Java -> JDBC
    }

    @Override
    public Date getNullableResult(ResultSet rs, String columnName) throws SQLException {
        Timestamp sqlTimestamp = rs.getTimestamp(columnName);
        return sqlTimestamp != null ? new Date(sqlTimestamp.getTime()) : null; // JDBC -> Java
    }

    @Override
    public Date getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        Timestamp sqlTimestamp = rs.getTimestamp(columnIndex);
        return sqlTimestamp != null ? new Date(sqlTimestamp.getTime()) : null;
    }

    @Override
    public Date getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        Timestamp sqlTimestamp = cs.getTimestamp(columnIndex);
        return sqlTimestamp != null ? new Date(sqlTimestamp.getTime()) : null;
    }
}
```

---

### 2. 自定义 EnumTypeHandler

```java
public class EnumTypeHandler<E extends Enum<E>> extends BaseTypeHandler<E> {
    private final Class<E> type;

    public EnumTypeHandler(Class<E> type) {
        this.type = type;
    }

    @Override
    public void setParameter(PreparedStatement ps, int i, E parameter, JdbcType jdbcType)
        throws SQLException {
        ps.setString(i, parameter.name());
    }

    @Override
    public E getResult(ResultSet rs, String columnName) throws SQLException {
        String value = rs.getString(columnName);
        return value == null ? null : Enum.valueOf(type, value);
    }

    @Override
    public E getResult(ResultSet rs, int columnIndex) throws SQLException {
        String value = rs.getString(columnIndex);
        return value == null ? null : Enum.valueOf(type, value);
    }

    @Override
    public E getResult(CallableStatement cs, int columnIndex) throws SQLException {
        String value = cs.getString(columnIndex);
        return value == null ? null : Enum.valueOf(type, value);
    }
}
```

---

### 3. MyBatis XML 配置

```xml
<typeHandlers>
    <typeHandler javaType="com.example.MyEnum"
                 handler="com.example.EnumTypeHandler"/>
</typeHandlers>
```

---

## 面试速记

- **核心机制**：MyBatis 通过 **TypeHandler** 实现 Java 类型和 JDBC 类型的双向转换
- **工作原理**：
  - `setParameter`：Java 类型 → JDBC 类型（写入数据库）
  - `getResult`：JDBC 类型 → Java 类型（读取数据库）
- **内置 TypeHandler**：
  - 常见类型已有默认实现（Integer、String、Date...）
  - 注册在 `TypeHandlerRegistry` 中
- **自定义 TypeHandler**：
  - 继承 `BaseTypeHandler<T>`
  - 重写 `setParameter` 和 `getResult` 系列方法
  - 典型场景：处理枚举类型、自定义对象、特殊格式的日期
- **配置方式**：
  - Java 注解方式（@MappedTypes, @MappedJdbcTypes）
  - XML 配置 `<typeHandlers>`
- **高频考点**：
  - 为什么需要 TypeHandler？（解决 Java 类型和数据库类型不一致的问题）
  - 如果数据库中的字段是 JSON/枚举/自定义格式，如何映射？（自定义 TypeHandler）
  - MyBatis 内置 TypeHandler 的使用场景？

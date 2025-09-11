---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 一对一、一对多关联查询

## 实现方式

- 主要通过 **resultMap**
- 两种方式：
  1. 嵌套结果映射 (Nested Result Mapping)
  2. 嵌套查询 (Nested Select)

## 嵌套结果映射 (Nested Result Mapping)

- 一次 SQL 查询同时返回主表和关联表数据
- **<association>** 表示一对一关系
- **<collection>** 表示一对多关系

### 示例

- 一对一：

```xml
<association property="user" javaType="User">
    <id column="userId" property="id"/>
    <result column="username" property="username"/>
</association>
```

- 一对多：

```xml
<collection property="orders" ofType="Order">
    <id column="orderId" property="id"/>
    <result column="orderName" property="orderName"/>
</collection>
```

## 嵌套查询 (Nested Select)

- 主查询只查询主表数据
- 关联表数据通过单独 SQL 获取
- **<association> 或 <collection>** 的 `select` 属性指定子查询

### 示例

- 一对一：

```xml
<association property="user" column="userId" select="getUserById"/>
```

- 一对多：

```xml
<collection property="orders" column="id" select="getOrdersByUserId"/>
```

## 面试速记

- **Q1: resultMap 的作用？**

  - 映射 SQL 查询结果到对象属性，支持复杂关联关系

- **Q2: 嵌套结果映射 vs 嵌套查询？**

  - 嵌套结果映射：一次 SQL 查询，性能高，但复杂 SQL 需 JOIN
  - 嵌套查询：主查询 + 子查询，SQL 可拆分，可能产生 N+1 查询

- **Q3: 如何实现一对一关系？**

  - `<association>` 标签

- **Q4: 如何实现一对多关系？**

  - `<collection>` 标签

- **Q5: 性能考虑？**

  - 嵌套结果映射适合数据量小/中
  - 嵌套查询可用于延迟加载，避免一次查询过大

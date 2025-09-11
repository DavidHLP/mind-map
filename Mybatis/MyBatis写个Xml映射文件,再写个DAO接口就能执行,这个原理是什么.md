---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 写 XML 映射文件 + DAO 接口执行原理

## 核心原理

- 基于 **JDBC** 能力 + **动态代理**
- 解析 XML 映射文件 + 动态生成 DAO 接口实现类 → 执行 SQL

## 加载配置和 Mapper 映射文件

- 启动时，通过 `mybatis-config.xml` 加载数据库连接信息和 Mapper.xml
- Mapper.xml 中的 SQL 被解析为 **MappedStatement** 对象
  - 包含 SQL 语句、参数和返回结果映射规则

## 动态代理实现 DAO 接口

- 为每个 DAO 接口生成 **MapperProxy** 动态代理类
- 拦截接口方法调用
- MapperProxy 根据方法名和参数匹配 MappedStatement，再调用 JDBC 执行 SQL

## 通过 JDBC 执行 SQL

- **SqlSession** 封装 JDBC
- 使用 **PreparedStatement** 执行 SQL
- XML 中定义的 SQL + DAO 方法参数 → 完整 SQL
- 参数绑定到 PreparedStatement → 执行 SQL → 获取 ResultSet

## 结果映射

- JDBC ResultSet 被 MyBatis 的 **ResultMap** 或 **resultType** 映射为 DAO 方法返回值类型
- 返回类型可为 POJO、Map、List 等

## 面试速记

- **Q1: MyBatis 执行 SQL 的核心原理？**
  - JDBC + 动态代理
- **Q2: Mapper.xml 在执行中作用？**
  - SQL 语句 + 参数 + 返回结果映射 → 封装为 MappedStatement
- **Q3: DAO 接口如何执行？**
  - MapperProxy 动态代理拦截方法 → 匹配 MappedStatement → 调用 JDBC
- **Q4: SQL 参数如何传入？**
  - 绑定到 PreparedStatement 占位符
- **Q5: SQL 执行结果如何映射？**
  - ResultSet → ResultMap 或 resultType → DAO 方法返回值

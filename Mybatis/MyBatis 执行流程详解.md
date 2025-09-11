---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 执行流程详解

---

## 1. 初始化阶段

- ### 加载配置文件
  - 加载 `mybatis-config.xml` 和 Mapper XML 文件。
- ### 构建 Configuration 对象
  - 封装所有配置信息，MyBatis 的核心。
- ### 注册 Mapper 接口
  - 创建 **MapperProxyFactory**，与 XML 关联。
  - 存储在 Configuration 对象的 `knownMappers` 中。
- ### 创建 SqlSessionFactory
  - 工厂模式，用于生产 `SqlSession`。

---

## 2. 获取 SqlSession

- ### 调用 `SqlSessionFactory.openSession()`
  - 获取 `SqlSession` 实例。

---

## 3. 获取 Mapper 代理对象

- ### 调用 `SqlSession.getMapper()`
  - 通过 `MapperRegistry` 查找 `MapperProxyFactory`。
- ### JDK 动态代理
  - 创建 **MapperProxy** 代理对象。
  - 所有 Mapper 接口方法调用都由 `MapperProxy` 拦截。

---

## 4. 执行 SQL

- ### 调用 Mapper 代理方法
  - 触发 `MapperProxy` 的 `invoke()` 方法。
- ### 解析方法信息
  - 根据方法名和参数，找到对应的 `MappedStatement`。
- ### 创建 Handler 对象
  - **StatementHandler**: 处理 SQL 语句。
  - **ParameterHandler**: 设置 SQL 参数。
- ### Executor 执行器
  - **Executor**: MyBatis 的核心执行器，负责 SQL 的执行。
- ### 处理结果集
  - **ResultSetHandler**: 将结果集封装成 Java 对象。

---

## 5. 返回结果

- ### 结果返回
  - `Executor` 将结果返回给 `MapperProxy`。
- ### 最终返回
  - `MapperProxy` 将结果返回给应用程序。

---

## 6. 面试速记

- **初始化**

  - 读取配置文件 → 生成 `Configuration` → 注册 Mapper → 创建 `SqlSessionFactory`。

- **获取会话**

  - `SqlSessionFactory.openSession()` → 拿到 `SqlSession`。

- **获取 Mapper**

  - `SqlSession.getMapper()` → JDK 动态代理生成 `MapperProxy`。

- **执行 SQL**

  - `MapperProxy.invoke()` → 找到 `MappedStatement` →

    - `StatementHandler` 处理 SQL
    - `ParameterHandler` 设置参数
    - `Executor` 调用数据库
    - `ResultSetHandler` 封装结果

- **返回结果**

  - `Executor` → `MapperProxy` → 调用方。

- **一句话概括**
  - **配置加载 → 会话创建 → Mapper 代理 → SQL 执行 → 结果映射 → 返回结果。**

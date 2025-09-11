---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 插件原理与开发指南

---

## 1. MyBatis 插件核心原理

- ### 动态代理
  - MyBatis 插件机制通过 **动态代理** 实现。
  - 主要在执行 SQL 的关键点（如执行查询、更新、插入等）拦截操作并增强功能。
- ### 拦截器拦截点
  - MyBatis 插件只允许拦截以下四种类型的对象：
    - **Executor**: 负责执行 CRUD 操作。
    - **ParameterHandler**: 负责处理 SQL 语句的参数。
    - **ResultSetHandler**: 负责处理查询结果集。
    - **StatementHandler**: 负责处理 SQL 语句。
- ### 拦截器链（InterceptorChain）
  - 当 MyBatis 启动时，会根据配置加载插件，并在需要拦截的地方生成代理对象。
  - 当被拦截的方法被调用时，MyBatis 会调用插件的 `intercept()` 方法。
  - 如果有多个插件，它们会形成一个 **拦截器链**，按照配置顺序依次执行。
  - `InterceptorChain` 通过 **JDK 动态代理** 得到代理对象，调用 `proceed()` 方法会执行下一个拦截器或目标方法。

---

## 2. 如何编写一个 MyBatis 插件

- ### 步骤
  1.  **实现 Interceptor 接口**:
      - 编写一个类，实现 MyBatis 的 `Interceptor` 接口。
      - 定义拦截逻辑。
  2.  **定义拦截逻辑（`intercept()` 方法）**:
      - 在 `intercept()` 方法中实现增强逻辑。
      - 使用 `Invocation` 对象调用目标方法。
      - 通过 `invocation.proceed()` 调用下一个拦截器或目标方法。
  3.  **配置插件**:
      - 在 `mybatis-config.xml` 文件中配置插件类和参数。
- ### `Interceptor` 接口的三个方法
  - `intercept(Invocation invocation)`:
    - 核心方法，实现拦截逻辑。
    - `Invocation` 对象包含了目标对象、方法和参数等信息。
  - `plugin(Object target)`:
    - 创建当前拦截对象的代理对象。
    - 返回 `Plugin.wrap(target, this)`。
  - `setProperties(Properties properties)`:
    - 用于从配置中获取属性。
    - 插件配置中 `<property>` 标签定义的参数会传递到这里。
- ### `Signature` 注解
  - `@Intercepts` 注解用于声明要拦截的类和方法。
  - `@Signature` 注解用于定义具体的拦截点，包括：
    - `type`: 目标类，如 `StatementHandler.class`。
    - `method`: 目标方法名，如 `"prepare"`。
    - `args`: 目标方法的参数类型数组，如 `{Connection.class, Integer.class}`。

---

## 3. 插件常见用途

- **性能分析**: 统计 SQL 的执行时间，输出到日志中。
- **动态 SQL 注入**: 在 `ParameterHandler` 中修改或添加 SQL 参数。
- **数据脱敏**: 在 `ResultSetHandler` 中对查询结果进行数据处理。
- **自动分页**: 在 `StatementHandler` 中拦截 SQL，自动添加分页逻辑。

---

## 4. 示例代码

- ### 插件类 `ExamplePlugin.java`

  ```java
  @Intercepts({
      @Signature(type=StatementHandler.class, method="prepare", args={Connection.class, Integer.class})
  })
  public class ExamplePlugin implements Interceptor {
      @Override
      public Object intercept(Invocation invocation) throws Throwable {
          // 获取代理对象
          StatementHandler statementHandler = (StatementHandler) invocation.getTarget();
          // 获取 SQL 语句
          String sql = statementHandler.getBoundSql().getSql();
          System.out.println("SQL: " + sql);
          // 继续执行其他插件或目标方法
          return invocation.proceed();
      }

      @Override
      public Object plugin(Object target) {
          // 创建代理对象
          return Plugin.wrap(target, this);
      }

      @Override
      public void setProperties(Properties properties) {
          // 从配置中获取属性
          String someProperty = properties.getProperty("someProperty");
          System.out.println("Some Property: " + someProperty);
      }
  }
  ```

- ### MyBatis 配置 `mybatis-config.xml`

  ```xml
  <plugins>
      <plugin interceptor="com.example.ExamplePlugin">
          <property name="someProperty" value="value"/>
      </plugin>
  </plugins>
  ```

---

## 面试速记

- **核心原理**: 基于 **JDK 动态代理**。MyBatis 在运行时为核心对象（如 Executor、StatementHandler）生成代理对象，插件通过代理对象拦截方法调用。
- **拦截点**: 只能拦截四种核心对象：**Executor、StatementHandler、ParameterHandler、ResultSetHandler**。
- **拦截器链**: 如果配置了多个插件，它们会形成一个 **责任链**，按配置顺序依次执行。通过 `invocation.proceed()` 方法将执行权传递给下一个拦截器或目标方法。
- **编写步骤**:
  1.  创建一个类，**实现 `Interceptor` 接口**。
  2.  用 **`@Intercepts` 和 `@Signature` 注解** 声明要拦截的类和方法。
  3.  在 **`intercept()` 方法** 中编写核心拦截逻辑，并调用 `invocation.proceed()`。
  4.  在 **`plugin()` 方法** 中返回 `Plugin.wrap(target, this)` 来创建代理。
  5.  在 **`setProperties()` 方法** 中接收配置参数。
- **常见用途**: **性能监控**（统计 SQL 执行时间）、**动态 SQL 注入**（修改参数）、**数据脱敏**、**自动分页**。
- **关键方法**:
  - `intercept(Invocation)`: 核心拦截逻辑。
  - `plugin(Object)`: 生成代理对象。
  - `setProperties(Properties)`: 获取配置参数。

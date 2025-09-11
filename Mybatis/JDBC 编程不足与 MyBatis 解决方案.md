---
markmap:
  colorFreezeLevel: 2
---

# JDBC 编程不足与 MyBatis 解决方案

## JDBC 不足之处

### 1. 资源管理复杂

- 需要手动管理 Connection、PreparedStatement、ResultSet
- 必须手动关闭，容易遗漏导致资源泄露
- ```java
  // JDBC 示例 - 资源管理繁琐
  Connection conn = null;
  PreparedStatement ps = null;
  ResultSet rs = null;
  try {
      conn = DriverManager.getConnection(url, user, password);
      ps = conn.prepareStatement("SELECT id, name FROM user WHERE id=?");
      ps.setInt(1, 1);
      rs = ps.executeQuery();
      if(rs.next()) {
          System.out.println(rs.getString("name"));
      }
  } finally {
      if(rs != null) rs.close();
      if(ps != null) ps.close();
      if(conn != null) conn.close();
  }
  ```

### 2. SQL 与代码耦合

- SQL 写死在 Java 代码中，可读性差，维护困难

### 3. 结果集处理繁琐

- 需要手动从 ResultSet 提取字段并映射到对象
- 多表关联查询时映射逻辑复杂

### 4. 参数处理麻烦

- 需要手动设置参数，必须知道字段类型
- 容易出错（比如索引错位、类型不匹配）

### 5. 事务管理复杂

- 事务开启、提交、回滚都需要手动处理
- ```java
    try {
        conn.setAutoCommit(false);
        // SQL 操作
        conn.commit();
    } catch (Exception e) {
        conn.rollback();
    }
  ```

## MyBatis 解决方案

### 1. 简化资源管理

- 使用 `SqlSession` 管理连接和资源，减少样板代码
- ```java
  try(SqlSession session = sqlSessionFactory.openSession()) {
      User user = session.selectOne("UserMapper.selectUser", 1);
      System.out.println(user.getName());
  }
  ```

### 2. SQL 与代码分离

- SQL 可放在 XML 中，或用注解声明
- ```xml
  <!-- UserMapper.xml -->
  <select id="selectUser" parameterType="int" resultType="User">
      SELECT id, name FROM user WHERE id = #{id}
  </select>
  ```

### 3. 自动结果映射

- MyBatis 自动将查询结果映射到 Java 对象或集合
- 支持一对一、一对多、嵌套映射

### 4. 参数绑定与类型转换

- 使用 `#{}` 占位符，自动识别类型
- 避免拼接 SQL，防止 SQL 注入

### 5. 简化事务管理

- 与 Spring 集成后可使用声明式事务管理
- ```java
  @Service
  public class UserService {
      @Autowired private UserMapper userMapper;

      @Transactional
      public void updateUser(User user) {
          userMapper.updateUser(user);
      }
  }
  ```

### 6. 内置缓存机制

- 一级缓存（SqlSession 级别）
- 二级缓存（Mapper 级别，可配置）

---

## JDBC 与 MyBatis 对比

| 特性           | JDBC                            | MyBatis                          |
| -------------- | ------------------------------- | -------------------------------- |
| 资源管理       | 手动管理连接和关闭，繁琐        | `SqlSession` 自动管理，简化操作  |
| SQL 与代码分离 | SQL 硬编码在 Java 中，维护困难  | SQL 可放 XML/注解，清晰易维护    |
| 参数绑定       | 手动设置参数，易错              | `#{}` 自动绑定，安全可靠         |
| 结果映射       | 手动从 ResultSet 提取并封装对象 | 自动映射到对象，支持复杂嵌套关系 |
| 事务管理       | 手动管理事务，易遗漏            | 可与 Spring 集成，支持声明式事务 |
| 缓存机制       | 无                              | 一级/二级缓存，性能更优          |

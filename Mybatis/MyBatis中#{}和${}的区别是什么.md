---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 中 #{} 和 ${} 的区别

## #{} (PreparedStatement 占位符)

- 是 PreparedStatement 占位符
- 对参数做类型处理，借助 PreparedStatement 绑定
- 防止 SQL 注入
- 参数作为参数传递给数据库，而非直接拼接进 SQL
- 优点：
  - **高效**：SQL 语句仅编译一次，复用执行计划
  - **安全**：参数绑定，防止 SQL 注入
- 类比：预编译菜单，只需提供细节参数即可

## ${} (字符串拼接)

- 直接把参数拼接到 SQL 中
- 不做类型处理，也不使用 PreparedStatement
- 用途：动态表名、列名、排序等预编译无法满足的场景
- 示例：
  - 动态列名：
    ```xml
    <select id="selectByColumn" resultType="User">
      SELECT * FROM users WHERE ${column} = #{value}
    </select>
    ```
  - 动态排序：
    ```xml
    <select id="selectAllUsers" resultType="User">
      SELECT * FROM users ORDER BY ${orderByColumn} ${orderByType}
    </select>
    ```

## 面试速记

- **Q1: #{} 与 ${} 最大区别？**
  - `#{}`：参数绑定，防止 SQL 注入
  - `${}`：字符串拼接，直接拼入 SQL
- **Q2: #{} 使用场景？**
  - 普通查询、更新、插入、删除等参数化 SQL
- **Q3: ${} 使用场景？**
  - 动态表名、动态列名、动态排序等无法预编译的场景
- **Q4: 安全性区别？**
  - `#{}` 安全，`${}` 可能导致 SQL 注入
- **Q5: 性能区别？**
  - `#{}` 预编译高效，`${}` 每次执行都重新解析 SQL

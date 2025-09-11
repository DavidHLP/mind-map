---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 动态 SQL

## 有什么用

- 根据不同条件动态生成 SQL
- 提高 SQL 的灵活性和复用性
- 适用于复杂查询或更新场景
- 根据参数动态构建不同 SQL

## 执行原理

- 基于 XML 映射文件中的 SQL 片段与标签
- 常见标签：
  - if
  - choose / when / otherwise
  - where
  - foreach
- 运行时根据参数值解析标签 → 生成完整 SQL → 执行

## 执行流程

1. **解析动态 SQL**
   - MyBatis 解析 XML 文件中的动态 SQL 标签
2. **参数绑定**
   - 执行 SQL 时绑定传入参数值
3. **生成最终 SQL**
   - 根据参数值动态拼接完整 SQL 语句
4. **执行 SQL**
   - 发送到数据库并返回结果

## 面试速记

- **Q1: 什么是动态 SQL？**
  - 按条件动态拼接 SQL 语句，避免硬编码。
- **Q2: 常用标签有哪些？**
  - `if`、`choose/when/otherwise`、`where`、`foreach`。
- **Q3: 动态 SQL 的执行过程？**
  - 解析 XML → 绑定参数 → 生成完整 SQL → 执行。
- **Q4: 动态 SQL 的优势？**
  - 提高 SQL 灵活性，减少冗余，代码更简洁。

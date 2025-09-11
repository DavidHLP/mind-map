---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 与 Hibernate 的区别

## Hibernate

- 完整 ORM 框架
- 面向对象操作数据库，实现对象模型与关系模型映射
- 屏蔽不同数据库的 SQL 差异
- 提供 API、HQL、Criteria 接口操作数据库
- 支持一、二级缓存，提高程序可移植性
- 自动生成 SQL，无法精细控制（如索引选择）
- 增加 SQL 转化层，可能影响性能
- 国外广泛使用，国内多使用 MyBatis

## MyBatis

- 半 ORM 框架
- 需手动编写 SQL，更灵活轻量
- 支持动态 SQL，极端情况下性能更优
- 与底层数据库强绑定，更换数据库需重写 SQL
- Hibernate 无需写 SQL，适合项目简单、QPS 不高、快速开发
- MyBatis 适合核心服务、高 QPS、精细化 SQL 性能优化

## 面试速记

- **Q1: Hibernate 和 MyBatis 最大区别？**
  - Hibernate 自动生成 SQL，MyBatis 需手动编写 SQL
- **Q2: Hibernate 优势？**
  - 完整 ORM、屏蔽数据库差异、提供缓存、快速开发
- **Q3: Hibernate 劣势？**
  - SQL 控制不精细，可能影响性能
- **Q4: MyBatis 优势？**
  - 灵活轻量、动态 SQL、性能可优化
- **Q5: MyBatis 劣势？**
  - 强绑定数据库，更换数据库需改 SQL
- **Q6: 适用场景？**
  - Hibernate：简单项目、QPS 低、快速开发
  - MyBatis：核心服务、高 QPS、追求 SQL 性能优化

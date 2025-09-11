---
markmap:
  colorFreezeLevel: 2
---

# MySQL 中的事务隔离级别

## 读未提交 (READ UNCOMMITTED)

- 一个事务可以看到另一个事务未提交的数据修改
- **存在脏读问题**

## 读已提交 (READ COMMITTED)

- 一个事务只能看到已经提交事务的数据
- **防止脏读，但存在不可重复读**
- 不可重复读示例：
  - 事务 A 第一次读用户余额=100
  - 事务 B 修改该用户余额=200 并提交
  - 事务 A 第二次读结果可能为 200（不可重复读）

## 可重复读 (REPEATABLE READ)

- 确保在同一事务中多次查询结果一致
- **避免不可重复读，但可能存在幻读**
- 幻读示例：
  - 事务 A 第一次查询余额<200，返回 1 条记录
  - 事务 B 插入新记录并提交
  - 事务 A 第二次查询可能返回 2 条记录

## 串行化 (SERIALIZABLE)

- 执行保证可串行化
- 避免所有并发问题，但大幅降低并发性能

## 面试速记

- **Q1: 事务隔离级别从低到高顺序？**
  - READ UNCOMMITTED → READ COMMITTED → REPEATABLE READ → SERIALIZABLE
- **Q2: READ UNCOMMITTED 问题？**
  - 脏读
- **Q3: READ COMMITTED 问题？**
  - 不可重复读
- **Q4: REPEATABLE READ 问题？**
  - 幻读
- **Q5: SERIALIZABLE 优缺点？**
  - 优点：避免所有并发问题
  - 缺点：降低并发性能
- **Q6: InnoDB 默认隔离级别？**
  - REPEATABLE READ

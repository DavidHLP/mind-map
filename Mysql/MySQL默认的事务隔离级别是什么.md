---
markmap:
  colorFreezeLevel: 2
---

# MySQL 默认事务隔离级别及 binlog 影响

## 默认隔离级别

- MySQL 默认隔离级别：**可重复读 (REPEATABLE READ, RR)**

## 原因

- 为兼容早期 **binlog statement 格式**
- 若采用读已提交 (RC) 或读未提交 (RU) 隔离级别，搭配 statement 格式 binlog，可能导致主从数据库数据不一致

## 隔离级别与 binlog 格式对主从一致性影响

1. **核心概念**

   - **binlog**：主库记录数据变更日志，从库通过其同步数据
     - `statement` 格式：记录 SQL 语句本身，依赖执行环境一致
   - **事务隔离级别**：控制事务间数据可见性
     - RU/RC：易受其他事务提交影响
     - RR/Serializable：保证读取数据稳定（RR 用一致性快照，Serializable 强制事务串行）

2. **不兼容根源**

   - `statement` 格式依赖主从执行 SQL 的环境一致
   - RU/RC 隔离级别下，同一 SQL 在主从可能读取不同数据 → 结果不一致

3. **高隔离级别兼容原因**

   - RR：一致性快照，读取不受其他事务影响
   - Serializable：事务串行执行
   - 保证主从执行环境一致，避免差异

4. **结论与建议**
   - `statement` 格式仅兼容 RR / Serializable
   - 现代数据库推荐 **row 格式 binlog**，记录修改的数据行，从根本上规避问题

## 面试速记

- **Q1: MySQL 默认事务隔离级别？**
  - REPEATABLE READ (RR)
- **Q2: 为什么选择 RR？**
  - 避免 RU/RC 在 statement binlog 下导致主从不一致
  - RR 提供一致性快照，确保事务读取稳定
- **Q3: statement vs row binlog？**
  - statement：记录 SQL，依赖环境一致，RU/RC 易出问题
  - row：记录数据行变更，兼容性更高
- **Q4: 主从复制建议隔离级别？**
  - 使用 RR 或 Serializable，或改用 row 格式 binlog

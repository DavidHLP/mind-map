# MySQL 事务实现原理

## 核心组成

1. **锁（Locks）**
   - 控制并发访问，保证事务隔离性
   - 包括 **行锁、间隙锁** 等
2. **Redo Log（重做日志）**
   - 记录事务对数据库的所有修改
   - 崩溃恢复时，通过重放 Redo Log 恢复数据
   - 保障事务的 **持久性**
3. **Undo Log（回滚日志）**
   - 记录事务的反向操作，保存历史版本
   - 事务回滚时使用
   - 保障事务的 **原子性** 和 **隔离性**
4. **MVCC（多版本并发控制）**
   - 通过创建数据快照，实现 **非锁定读**
   - 提高并发度
   - 支持 **读已提交（Read Committed）** 和 **可重复读（Repeatable Read）**
5. **事务 ACID 特性**
   - **A** 原子性（Atomicity） → Undo Log 回滚
   - **C** 一致性（Consistency） → 事务提交前后数据保持一致
   - **I** 隔离性（Isolation） → 锁 + MVCC
   - **D** 持久性（Durability） → Redo Log

---

## 面试速记

- **锁** → 隔离性
- **Redo Log** → 持久性
- **Undo Log** → 原子性 + 隔离性
- **MVCC** → 隔离性 + 非锁定读 + 高并发
- **ACID** → MySQL 事务依赖 **Redo + Undo + 锁 + MVCC**

# MySQL 的 Change Buffer

## 是什么

- InnoDB 内存池中的一块缓冲区域

## 作用

- 缓存二级索引的修改
- 减少二级索引页修改产生的随机 I/O

## 作用对象

- 二级索引

## 不生效对象

- 主键索引
- 全文索引
- 空间索引
- 唯一索引（需读取数据页检查唯一性，与 Change Buffer 设计目的不符）

## 数据安全

- 通过系统恢复日志保证数据安全
- redo log 记录 Change Buffer 的修改，保证数据一致性

## 参数

- `innodb_change_buffer_max_size`：配置 Change Buffer 大小
  - 默认 25%（相对于 InnoDB 缓冲池）
  - 最大可设置为 50%

## 面试速记

- **Q1: Change Buffer 是什么？**
  - InnoDB 内存池的一块缓冲区域
- **Q2: Change Buffer 作用对象？**
  - 二级索引
- **Q3: 哪些索引不适用 Change Buffer？**
  - 主键、全文、空间、唯一索引
- **Q4: 优势？**
  - 减少二级索引随机 I/O，提高写性能
- **Q5: 数据安全如何保证？**
  - 通过 redo log 和系统恢复日志
- **Q6: Change Buffer 大小配置参数？**
  - `innodb_change_buffer_max_size`，默认 25%，最大 50%

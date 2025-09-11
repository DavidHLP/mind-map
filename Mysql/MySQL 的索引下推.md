---
markmap:
  colorFreezeLevel: 2
---

# MySQL 的索引下推（Index Condition Pushdown, ICP）

## 是什么

- 一种减少回表查询、提高查询效率的技术
- 将部分查询条件下推到存储引擎层过滤

## 作用

- 减少从表中读取的数据行
- 减少 IO
- 将原本在 Server 层处理的过滤操作交给存储引擎层

## 举例

- 表：people，索引 INDEX(zipcode, lastname, firstname)
- 查询：
  ```sql
  SELECT * FROM people
  WHERE zipcode='95054'
    AND lastname LIKE '%etru%'
  ```

* 没有 ICP：

  - 联合索引只能用 zipcode 条件
  - lastname 和 address 条件在 Server 层过滤

* 有 ICP：

  - 存储引擎层先用 zipcode 条件过滤
  - 然后对 lastname 条件再次过滤
  - 减少传给 Server 的数据量

## TIP

- 支持版本：MySQL 5.6+
- 生效存储引擎：InnoDB、MyISAM
- 子查询、函数/表达式、聚簇索引（主键）可能导致 ICP 不生效
- 可通过 `EXPLAIN` 查看是否生效

## 面试速记

- **Q1: ICP 是什么？**

  - Index Condition Pushdown，将条件下推到存储引擎层

- **Q2: 优势？**

  - 减少回表次数，降低 IO，提高查询效率

- **Q3: 支持存储引擎？**

  - InnoDB、MyISAM

- **Q4: 不生效场景？**

  - 子查询、函数/表达式、聚簇索引查询

- **Q5: 如何验证？**

  - `EXPLAIN` 查看 Extra 列是否包含 "Index condition pushdown"

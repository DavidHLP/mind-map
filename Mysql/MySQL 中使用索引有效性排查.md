---
markmap:
  colorFreezeLevel: 2
---

# MySQL 中使用索引有效性排查

## 1. 索引不一定有效

- 查询条件中未包含索引列或低基数列索引效果差
- 查询条件顺序或组合与联合索引不匹配
- 小表可能选择全表扫描，因为全表扫描开销可能小于使用索引
- MySQL 根据成本（CPU + I/O）自动决定使用索引或全表扫描

---

## 2. 排查索引效果

### 使用 EXPLAIN

- 在 SQL 前加 `EXPLAIN` 查看执行计划

### 主要观察字段

- **type**：访问类型（ALL、index、range 等）
  - ALL → 全表扫描
  - index / range → 使用索引
- **key**：实际使用的索引名称
  - NULL → 没有使用索引
- **rows**：扫描的行数，评估扫描量

---

## 3. 索引失效的常见场景

### 3.1 数据量较小

- 全表扫描成本低于索引扫描

### 3.2 联合索引未符合最左前缀原则

- 只有最左列使用索引，右侧列无效

### 3.3 索引列使用运算

```sql
SELECT * FROM users WHERE age - 10 = 20;
```

### 3.4 索引列使用函数

```sql
SELECT * FROM users WHERE SUBSTR(email, 1, 4) = 'test';
```

### 3.5 LIKE 左模糊或左右模糊

```sql
SELECT * FROM users WHERE email LIKE '%@example.com';
```

### 3.6 OR 条件混合索引与非索引

```sql
SELECT * FROM users WHERE name = '李四' OR create_time = '2025-08-08';
```

### 3.7 字段类型隐式转换

```sql
SELECT * FROM users WHERE email = 123456;
```

### 3.8 参数差异导致索引不使用

- 优化器根据成本判断是否使用索引，复杂查询或参数不同可能选择全表扫描

### 3.9 表中字段间比较

```sql
SELECT * FROM users WHERE name = email;
```

### 3.10 ORDER BY 排序字段未命中索引

```sql
SELECT * FROM users WHERE name = '张三' ORDER BY email;
```

### 3.11 NOT IN / NOT EXISTS

- B+ 树索引擅长查找存在的值，对“不存在”的值非搜索参数（Non-SARGable）
- 通常导致全表扫描

### 3.12 IN 列表过长

- 当列表元素过多，优化器可能认为全表扫描成本低于多次索引查找

---

## 4. 面试速记

- **Q1: 如何判断索引是否生效？**

  - 使用 EXPLAIN，查看 type / key / rows

- **Q2: 索引失效常见原因？**

  - 联合索引未最左前缀、索引列运算/函数、左模糊匹配、OR 混合条件、字段类型转换

- **Q3: IN / NOT IN / NOT EXISTS 会影响索引吗？**

  - IN 列表过长可能放弃索引，NOT IN / NOT EXISTS 通常不使用索引

- **Q4: 小表是否必然使用索引？**

  - 不一定，小表可能全表扫描成本更低

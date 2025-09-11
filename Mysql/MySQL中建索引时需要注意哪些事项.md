---
markmap:
  colorFreezeLevel: 2
---

# MySQL 建索引注意事项

## 1. 建索引的原则

- **不能盲目建立索引**
  - 索引会占用空间
  - 数据修改时需要维护索引，增加资源消耗

---

## 2. 应该建立索引的场景

### (1) 频繁作为查询条件的字段

- WHERE 后经常查询的字段
- 多个条件经常一起查询，可考虑**联合索引**
- 例：

```sql
SELECT * FROM users WHERE first_name = '张' AND last_name = '三';
```

### (2) 排序、分组、去重字段

- ORDER BY、GROUP BY、DISTINCT 后的字段
- 索引可减少排序或分组的开销
- 例：

```sql
SELECT DISTINCT status FROM tasks ORDER BY create_time DESC;
```

---

## 3. 不建议建立索引的场景

### (1) 高重复字段

- 如性别、布尔值
- 重复值多，索引选择性低，查询加速效果不明显
- 例外：少量状态值用作过滤，仍可建立索引

### (2) 长字段

- 如 TEXT、LONGTEXT
- 占用大量内存，扫描慢，缓存效率低
- 性能提升可能不明显，甚至下降

---

## 4. 需要权衡的情况

### 修改频率远大于查询频率

- 索引会降低修改效率（INSERT/UPDATE/DELETE）
- 如果查询少而修改多，可能**得不偿失**

---

## 面试速记

1. 索引提升查询，但增加写入成本
2. WHERE、ORDER BY、GROUP BY、DISTINCT 字段优先建索引
3. 高重复值和长字段慎建索引
4. 联合索引遵循最左前缀原则
5. 修改频繁的表要权衡索引数量

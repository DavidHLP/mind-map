# MySQL 的索引类型

## 按数据结构分类

### B+树索引

- 通过树形结构存储数据
- 适用场景：
  - 范围查询：BETWEEN、>、<
  - 精确查询：=
  - 排序和聚合操作
- 默认索引类型：InnoDB / MyISAM

### Hash 索引

- 哈希表结构
- 优点：等值查询速度快
- 缺点：不支持范围查询，数据无序
- 常用：Memory 引擎
- 示例：
  ```sql
  CREATE INDEX idx_username_hash ON users(username) USING HASH;
  ```

### 倒排索引（全文索引 Full-Text）

- 用于全文搜索
- 支持模糊匹配和关键字搜索
- 适合大文本字段（TEXT）
- 示例：

  ```sql
  CREATE FULLTEXT INDEX idx_content ON articles(content);
  ```

### R-树索引（多维空间树）

- 多维空间数据专用（地理坐标）
- 用于空间查询，如最近距离、区域查询
- 示例：

  ```sql
  CREATE SPATIAL INDEX idx_location ON places(location);
  ```

## 按物理存储分类

### 聚簇索引（主键索引）

- B+树叶子节点存储实际数据

### 二级索引（辅助索引）

- B+树叶子节点存储主键值
- 查询过程可能需要“回表”

### 扩展：覆盖索引

- 查询可直接在二级索引中获取数据
- 避免回表，提高效率

## 按字段特性分类

### 主键索引

- 建立在主键字段上
- 唯一且非空
- 示例：

  ```sql
  PRIMARY KEY (index_column_1) USING BTREE
  ```

### 唯一索引

- 建立在 UNIQUE 字段
- 值唯一，允许 NULL
- 示例：

  ```sql
  UNIQUE KEY(index_column_1,index_column_2,...)
  CREATE UNIQUE INDEX index_name
  ON table_name(index_column_1,index_column_2,...);
  ```

### 普通索引

- 普通字段建立
- 示例：

  ```sql
  INDEX(index_column_1,index_column_2,...)
  CREATE INDEX index_name
  ON table_name(index_column_1,index_column_2,...);
  ```

### 前缀索引

- 对字符字段的前 N 个字符建立索引
- 适用字段：char、varchar、binary、varbinary
- 示例：

  ```sql
  INDEX(column_name(length))
  CREATE INDEX index_name
  ON table_name(column_name(length));
  ```

### 全文索引

- 用于文本搜索、模糊匹配
- 示例：

  ```sql
  CREATE FULLTEXT INDEX idx_content ON articles(content);
  ```

### 空间索引

- 空间数据查询（经纬度）
- 使用 R 树
- 示例：

  ```sql
  CREATE SPATIAL INDEX idx_location ON places(location);
  ```

## 按字段个数分类

### 单列索引

- 建立在单列上，如主键索引

### 联合索引（复合索引）

- 多字段组合索引
- 示例：

  ```sql
  CREATE INDEX index_product_no_name ON product(product_no, name);
  ```

## 面试速记 - MySQL 索引类型

### Q1: 按数据结构分类有哪些索引？

- **B+树索引**：范围查询、精确查询、排序聚合，默认类型
- **Hash 索引**：等值查询快，不支持范围，Memory 引擎
- **倒排索引（全文索引）**：全文搜索，支持模糊匹配
- **R-树索引**：多维空间数据（GIS）查询

### Q2: 聚簇索引和二级索引区别？

- **聚簇索引**：叶子节点存实际数据，数据按主键顺序
- **二级索引**：叶子节点存主键值，查询可能需要回表

### Q3: 覆盖索引作用？

- 查询字段全在二级索引中，无需回表
- 减少 IO，提升查询效率

### Q4: 按字段特性分类的主要索引？

- **主键索引**：唯一且非空，一张表只能有一个
- **唯一索引**：值唯一，可有多个 NULL
- **普通索引**：普通字段索引
- **前缀索引**：字符字段前 N 个字符
- **全文索引**：文本搜索
- **空间索引**：空间数据查询（R 树）

### Q5: 单列索引 vs 联合索引？

- **单列索引**：单字段建立索引，如主键
- **联合索引**：多个字段组合，遵循最左匹配原则

### Q6: 创建示例？

```sql
-- B+ 树索引
CREATE INDEX idx_name ON table_name(name) USING BTREE;

-- Hash索引
CREATE INDEX idx_username_hash ON users(username) USING HASH;

-- 前缀索引
CREATE INDEX idx_prefix ON table_name(column_name(10));

-- 全文索引
CREATE FULLTEXT INDEX idx_content ON articles(content);

-- 空间索引
CREATE SPATIAL INDEX idx_location ON places(location);

-- 联合索引
CREATE INDEX idx_prod_name ON product(product_no, name);
```

---
markmap:
  colorFreezeLevel: 2
---

# MyBatis 的缓存机制

## 一级缓存（SqlSession 级别）

- 默认开启，生命周期与 `SqlSession` 一致。
- 仅在同一个 `SqlSession` 中生效。
- 基于命名空间、SQL 语句和参数作为唯一标识。
- 执行 `commit`、`rollback` 或手动清理缓存时会清空。

## 二级缓存（Mapper 级别）

- 跨 `SqlSession` 共享缓存，基于 Mapper 的缓存。
- 需要手动配置开启（Mapper XML 文件中需要 `<cache/>`）。
- 生命周期与 `SqlSessionFactory` 一致。
- 数据的更新、插入、删除会使相关缓存失效。
- 支持定制化存储（如整合第三方缓存工具）。
- 默认情况下，二级缓存是关闭的，需要手动开启。
- 缓存原来为内存 Map

## 使用建议

- 单机环境：
  - 对于频繁查询且不常更新的数据，可以使用二级缓存。
- 分布式环境：
  - 第三方缓存： 一级缓存工具如 Redis、Memcached 等 ， 本地缓存 caffeine ， guava 缓存 等。
  - 注意缓存一致性问题 ， 强一致性的建议使用第三方缓存，弱一致性建议使用本地缓存。

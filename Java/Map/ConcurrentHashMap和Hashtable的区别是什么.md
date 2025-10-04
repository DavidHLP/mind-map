---
markmap:
  colorFreezeLevel: 2
---

# ConcurrentHashMap 与 Hashtable 的区别

## 核心对比

- 二者都是 **Java 中常用的线程安全哈希表实现**
- **主要区别在性能**，根本原因在于 **线程安全机制不同**

### Hashtable

- 使用 **单一锁机制（全表锁）**
  - 所有操作（插入、删除、查找）都通过一个全局锁 `synchronized` 实现线程安全
  - 同一时刻只能有一个线程访问整个表
- **缺点**：
  - 并发性能低
  - 无法实现高效的并发访问
- **优点**：
  - 实现简单，早期 Java 中的线程安全 Map 实现

### ConcurrentHashMap（Java 8 及之后）

- 使用 **CAS + synchronized** 实现线程安全
  - CAS（Compare-And-Swap）用于无锁写入操作
  - 若节点冲突，则对该桶的头节点加锁（synchronized）
- **锁粒度更细**
  - 仅锁定冲突节点所在的桶（bucket）
  - 不是整个表
- **优点**：
  - 高并发性能
  - 支持多线程同时读写不同桶
- **缺点**：
  - 实现复杂，内存占用稍高

---

## 扩展知识

### HashMap vs Hashtable

- **HashMap**：
  - 非线程安全
  - 性能高于 Hashtable
  - 允许 `null` 键和值
- **Hashtable**：
  - 线程安全（全表锁）
  - 不允许 `null` 键和值

---

## Hashtable 的历史背景

- 早期 Java 提供的线程安全哈希表实现
- 使用全表锁，性能较差
- 随着多核 CPU 与高并发需求增加，出现性能瓶颈
- Java 5 引入 **ConcurrentHashMap** 以解决此问题

---

## 适用场景

### Hashtable

- 线程数较少、性能要求不高
- 现代开发中几乎已被淘汰

### ConcurrentHashMap

- 高并发场景
- 多线程频繁读写的系统
- 常见应用：
  - 分布式缓存
  - Web 应用缓存
  - 高并发数据共享模块

---

## 其他线程安全集合类

- **Collections.synchronizedMap(Map)**
  - 对普通 Map 进行同步封装，提供线程安全操作
- **CopyOnWriteArrayList**
  - 适用于读多写少的场景，写操作时复制底层数组

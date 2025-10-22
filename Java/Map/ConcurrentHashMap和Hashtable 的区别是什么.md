---
markmap:
  colorFreezeLevel: 2
---

# ConcurrentHashMap 和 Hashtable 的区别是什么

## 回答重点

- 它们都是 Java 中常用的线程安全哈希表实现
- **性能差异主要源于线程安全实现方式的不同**
  - **Hashtable**
    - 使用单一锁机制（全表锁）
    - 所有操作（插入、删除、查找）都依赖 `synchronized`
    - 并发环境下效率低，读写都需要获取锁
  - **ConcurrentHashMap**
    - Java 8 中采用 `CAS + synchronized`
    - 插入新节点时：CAS 无锁写入
    - 冲突时：退化为 `synchronized`，仅锁定冲突节点的头结点
    - 锁粒度更细，支持更高并发性能

## 扩展知识

### HashMap vs Hashtable

- HashMap：非线程安全，效率更高
- Hashtable：线程安全（全表锁），性能较差

### Hashtable 的历史背景

- Java 早期的线程安全哈希表实现
- 通过全表锁保证安全
- 随着多核 CPU 和高并发需求，性能问题明显
- Java 5 引入 `ConcurrentHashMap` 以解决性能瓶颈

### 适用场景

- **Hashtable**
  - 性能较差
  - 适合线程数较少、性能要求不高的场景
  - 现代开发中已很少使用
- **ConcurrentHashMap**
  - 适合高并发场景
  - 典型应用：分布式缓存、Web 应用缓存、多线程读写系统

### 线程安全集合类

- `Hashtable`
- `ConcurrentHashMap`
- `Collections.synchronizedMap`
  - 对任意普通 `Map` 的同步封装
- `CopyOnWriteArrayList`

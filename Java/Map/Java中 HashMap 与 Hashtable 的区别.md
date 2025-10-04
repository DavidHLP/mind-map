---
markmap:
  colorFreezeLevel: 2
---

# Java 中 HashMap 与 Hashtable 的区别

## 1. 线程安全性

- **HashMap**
  - 非线程安全
  - 多线程环境下可能出现数据不一致问题
- **Hashtable**
  - 线程安全
  - 内部方法大多使用 `synchronized` 关键字进行同步
  - 保证并发安全性，但影响性能

## 2. 性能差异

- **HashMap**
  - 无同步开销
  - 单线程环境下性能更优
- **Hashtable**
  - 由于使用全局锁（方法级同步）
  - 即使不同操作也会被串行化
  - 性能低于 HashMap

## 3. 允许空值

- **HashMap**
  - 允许一个 `null` 键
  - 允许多个 `null` 值
- **Hashtable**
  - 不允许 `null` 键和 `null` 值
  - 否则抛出 `NullPointerException`

## 4. 继承结构

- **HashMap**
  - 继承自 `AbstractMap`
  - 实现 `Map` 接口
- **Hashtable**
  - 继承自 `Dictionary`（已废弃）
  - 后来也实现了 `Map` 接口
  - 属于较早的类，在 Java 2 之后被 HashMap 取代
  - 不推荐使用

## 5. 迭代器类型

- **HashMap**
  - 使用 **快速失败（fail-fast）** 的 `Iterator`
  - 迭代过程中若修改结构 → 抛出 `ConcurrentModificationException`
- **Hashtable**
  - 使用 **弱一致性（weakly consistent）** 的 `Enumerator`
  - 虽然不建议修改，但不会抛异常

## 6. 扩展知识：ConcurrentHashMap vs Hashtable

- **ConcurrentHashMap**
  - 是 Hashtable 的替代方案
  - 实现线程安全的同时提升并发性能
  - 使用 **分段锁机制**（Segmented Locking）
  - 避免全局锁性能瓶颈
  - 读操作无锁化
  - 写操作使用局部锁分段
  - 高并发环境下性能远优于 Hashtable

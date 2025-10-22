---
markmap:
  colorFreezeLevel: 2
---

# Java 中的 HashMap 和 Hashtable 有什么区别

## 回答重点

### 线程安全性

- HashMap：非线程安全，多线程下可能数据不一致
- Hashtable：线程安全，大部分方法用 `synchronized`

### 性能差异

- HashMap：无同步开销，单线程性能更优
- Hashtable：全局锁，性能低，操作会串行化

### 允许空值

- HashMap：允许 1 个 `null` 键，多次 `null` 值
- Hashtable：不允许 `null` 键和 `null` 值，插入会抛出 `NullPointerException`

### 继承结构

- HashMap：继承 `AbstractMap`，实现 `Map`
- Hashtable：继承 `Dictionary`（已废弃），实现 `Map`，较古老，不推荐使用

### 迭代器类型

- HashMap：快速失败（fail-fast）Iterator
  - 修改结构会抛 `ConcurrentModificationException`
- Hashtable：弱一致性 Enumerator
  - 修改不会抛异常，但不推荐这么做

---

## 扩展知识

### ConcurrentHashMap vs Hashtable

- ConcurrentHashMap：替代方案，线程安全 + 高并发性能
  - 读操作无锁
  - 写操作使用分段锁（JDK7）或 CAS+链表/红黑树（JDK8）
- Hashtable：全局锁，性能瓶颈明显

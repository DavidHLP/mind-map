---
markmap:
  colorFreezeLevel: 2
---

# ConcurrentHashMap 线程安全机制详解

## 核心设计原理

- **分段锁机制 (JDK1.7)**
  - Segment 数组
  - 每个 Segment 继承 ReentrantLock
  - 减少锁竞争粒度
- **CAS + synchronized (JDK1.8+)**
  - 取消 Segment 概念
  - 使用 Node 数组
  - CAS 操作 + synchronized 同步

## 数据结构

### Node 节点

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V val;
    volatile Node<K,V> next;
}
```

### TreeNode 节点

```java
static final class TreeNode<K,V> extends Node<K,V> {
    TreeNode<K,V> parent;
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    boolean red;
}
```

## 关键操作的线程安全实现

### PUT 操作

- **步骤 1: 计算 hash 值**
  ```java
  static final int spread(int h) {
      return (h ^ (h >>> 16)) & HASH_BITS;
  }
  ```
- **步骤 2: 定位数组位置**
  ```java
  Node<K,V>[] tab = table;
  int n = tab.length;
  int i = (n - 1) & hash;
  ```
- **步骤 3: CAS 尝试插入**
  ```java
  if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value, null)))
      break; // 成功插入
  ```
- **步骤 4: synchronized 同步块**
  ```java
  synchronized (f) {
      // 链表或红黑树操作
  }
  ```

### GET 操作

- **无锁读取**
  ```java
  Node<K,V> e = tabAt(tab, (n - 1) & h);
  while (e != null) {
      if (e.hash == h && ((k = e.key) == key ||
          (key != null && key.equals(k))))
          return e.val;
      e = e.next;
  }
  ```
- **volatile 保证可见性**
  - val 字段使用 volatile
  - next 指针使用 volatile

### SIZE 计算

- **CounterCell 数组**
  ```java
  @sun.misc.Contended
  static final class CounterCell {
      volatile long value;
  }
  ```
- **baseCount + counterCells**
  ```java
  long sum = baseCount;
  CounterCell[] as = counterCells;
  if (as != null) {
      for (CounterCell a : as)
          if (a != null) sum += a.value;
  }
  ```

## 扩容机制 (resize)

- **多线程协助扩容**
  - transferIndex 控制进度
  - 每个线程处理一段数组
- **ForwardingNode 标记**
  ```java
  static final class ForwardingNode<K,V> extends Node<K,V> {
      final Node<K,V>[] nextTable;
  }
  ```
- **双表切换**
  - 旧表标记 ForwardingNode
  - 新表逐步迁移数据

## 性能优化技术

### CAS 操作

- **tabAt() - 原子读取**
  ```java
  static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) {
      return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);
  }
  ```
- **casTabAt() - 原子更新**
  ```java
  static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i,
                                      Node<K,V> c, Node<K,V> v) {
      return U.compareAndSwapObject(tab, ((long)i << ASHIFT) + ABASE, c, v);
  }
  ```

### 内存屏障

- **volatile 语义**
  - 禁止重排序
  - 保证可见性
- **Unsafe 操作**
  - 直接内存访问
  - 原子性保证

### 缓存行填充

- **@Contended 注解**
  - 避免伪共享
  - 提升性能

## 与其他 Map 的对比

- **HashMap**
  - 非线程安全
  - 高性能单线程
- **Hashtable**
  - synchronized 方法
  - 性能较差
- **Collections.synchronizedMap**
  - 包装器模式
  - 粗粒度锁
- **ConcurrentHashMap**
  - 细粒度锁
  - 高并发性能

## 适用场景

- **高并发读写**
- **读多写少**
- **对性能要求高**
- **需要线程安全的 Map**

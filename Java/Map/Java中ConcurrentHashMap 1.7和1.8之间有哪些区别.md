# Java ConcurrentHashMap 1.7 与 1.8 区别

## 核心区别

- **JDK 1.7**
  - 使用 **分段锁 (Segment Lock)**
  - 每个 `Segment` 相当于一个独立的 HashMap
  - 默认 16 个 Segment，并发度最高 16
- **JDK 1.8**
  - 移除 `Segment`
  - 锁粒度细化为 **节点级别**
  - 使用 **CAS + synchronized** 控制同步
  - 支持 **链表 + 红黑树** 结构
  - 并发性能大幅提升

---

## 结构图解

### JDK 1.7

- ![1.7结构](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203435_mianshiya.png)
- 通过 key 的 hash 定位 Segment，再锁定该 Segment。
- Segment 继承自 `ReentrantLock`。
- Segment 数组初始化后 **不会扩容**，导致并发度固定。
- 每个 Segment 中是一个独立的 HashMap。
- ![1.7分段锁](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203444_mianshiya.png)

> 并发度 = Segment 数量；每个 Segment 一把锁。

---

### JDK 1.8

- ![1.8结构](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203455_mianshiya.png)
- 不再分段，锁粒度细化到 **Node 节点级别**。
- 每个 Node 可视为一把锁。
- 通过 `synchronized` 实现节点锁定。
- 插入逻辑：
  1. 计算 hash 下标；
  2. 若 Node 为空，通过 **CAS** 插入；
  3. 若存在 Node，则对 Node 头部加锁；
  4. 判断 key 相等则替换，否则追加 Node；
  5. 链表过长转为红黑树。

---

## 扩容机制区别

### JDK 1.7

- **基于 Segment 扩容**：
  - 每个 Segment 独立扩容；
  - 互不影响；
  - 由 Segment 自行维护负载因子。

### JDK 1.8

- **全局扩容**：

  - 取消 Segment，变为统一的 Node 数组；
  - 任何位置触发阈值都会引发全局扩容；
  - 基于 **CAS** 确保线程安全；
  - **渐进式扩容**：多个线程协助搬迁数据。

- ![渐进式扩容](https://pic.code-nav.cn/mianshiya/question_picture/1800715091727163393/yrjrC1S9_image_mianshiya.png)

- > 扩容思路：
  >
  > - `transferIndex` 控制分段搬迁；
  > - 线程通过 CAS 竞争下标；
  > - 逐步搬迁 Node，降低性能抖动。

---

## size 计算方式差异

### JDK 1.7

- 调用 `size()`：
  - 尝试 3 次无锁求和；
  - 若结果相同直接返回；
  - 否则加锁重新计算。
- ```java
  if (retries++ == RETRIES_BEFORE_LOCK) {
      for (int j = 0; j < segments.length; ++j)
          ensureSegment(j).lock(); // force creation
  }
  // 再累加数量
  ```

### JDK 1.8

- 采用 **LongAdder 思想**：

  - 使用 `baseCount` + `counterCells[]` 数组；
  - CAS 失败时使用线程对应的 CounterCell；
  - 最终结果：`size = baseCount + sum(counterCells)`；
  - 避免单点竞争，提高性能。

- ![LongAdder原理](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203519_mianshiya.png)
- ![addCount维护计数](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203527_mianshiya.png)

- > 分而治之：减少多线程对单变量的竞争。

---

## CAS 机制

- CAS（Compare-And-Swap）：

  - 一种无锁同步原语；
  - 比较内存值与预期值，相同则更新；
  - 被广泛用于 ConcurrentHashMap 的插入与扩容中。

- 参考：

  - [500. 什么是 Java 的 CAS 操作？](https://www.mianshiya.com/question/1780933295027023873)

# Java 中的 CopyOnWriteArrayList 是什么？

## 回答重点

- `CopyOnWriteArrayList` 是 Java 提供的一个 **线程安全的动态数组实现**，位于 `java.util.concurrent` 包中。
- 核心思想是 **写时复制（Copy-On-Write）**：
  - 每次执行写操作时，复制底层数组；
  - 在副本上修改，然后替换原数组。
- 优点：
  - **读操作无锁**，非常高效；
  - 适用于 **读多写少** 的并发场景。
- 缺点：
  - **写操作成本高**，涉及数组复制；
  - 内存占用大。

---

## 源码分析

### 核心结构与操作

```java
public class CopyOnWriteArrayList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {

    private transient volatile Object[] array;

    // 写操作
    public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }

    // 读操作
    private E get(Object[] a, int index) {
        return (E) a[index];
    }
}
```

---

### 源码解读

- **数组复制机制**

  - 写操作（如 `add()`）会复制原数组，创建一个新数组副本；
  - 在新数组中添加元素；
  - 用新数组替换旧数组；
  - 整个过程使用锁保护，避免写冲突；
  - 旧数组仍可供其他线程安全读取。

- **读操作无锁**

  - 读操作（如 `get()`）直接从当前 `array` 读取；
  - 因为 `array` 被 `volatile` 修饰；
  - 读操作不会被写操作干扰。

- **线程安全保证**

  - 写操作期间使用 `ReentrantLock`；
  - 读操作无需加锁；
  - 通过 **数据不可变性 + 可见性（volatile）** 实现安全并发。

---

## 写时复制（Copy-On-Write）机制详解

### 核心思想

- 写操作不直接修改原数据结构，而是复制一个副本，在副本上修改，修改完成后再替换。

### 具体流程

1. **读取当前数组**

   - 获取当前正在被读的数组。

2. **复制数组**

   - 创建一个副本（新数组），拷贝所有现有元素。

3. **在副本上修改**

   - 对新数组进行写操作（增加、删除等）。

4. **替换旧数组**

   - 通过 `setArray(newArray)` 替换为新副本；
   - 旧数组将不再使用，但仍可被读线程安全访问。

5. **读操作无锁访问**

   - 所有读线程读取 `volatile` 数组；
   - 保证可见性且不会被修改。

---

### 写时复制的示意图

- ![写时复制示意图](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/RHfGHdEF_image_mianshiya.png)

- 每次写入时创建数组副本，旧数组仍然可被安全读取，直到被替换。

---

## 优缺点分析

| 类型         | 优点                               | 缺点                   |
| ------------ | ---------------------------------- | ---------------------- |
| **读操作**   | 无锁、高效、线程安全               | 无                     |
| **写操作**   | 简单、线程安全                     | 成本高（复制整个数组） |
| **适用场景** | 读多写少，如缓存、白名单、配置快照 | 写频繁时性能下降       |

---

## 使用场景

- 配置快照（读多写少）
- 系统白名单、黑名单
- 在线用户列表
- 监听器、观察者集合（如 `CopyOnWriteArrayList<Listener>`）

---

## 扩展：Redis 中的写时复制思想

- 类似机制在 Redis 中也存在：

  - 在生成 **RDB 文件** 时，采用写时复制；
  - 通过 **操作系统的 COW（Copy-On-Write）** 技术；
  - 保证持久化期间主线程可继续处理请求。
    - 🔗 [Redis 在生成 RDB 文件时如何处理请求？](https://www.mianshiya.com/question/1780933295639392257)

---

## 总结

| 特性           | 描述                         |
| -------------- | ---------------------------- |
| **所属包**     | `java.util.concurrent`       |
| **线程安全**   | ✅ 是                        |
| **底层结构**   | `volatile Object[] array`    |
| **核心机制**   | 写时复制（Copy-On-Write）    |
| **读写特点**   | 读无锁，写加锁复制           |
| **最佳场景**   | 读多写少、实时一致性要求不高 |
| **不适用场景** | 写频繁、数据量大             |

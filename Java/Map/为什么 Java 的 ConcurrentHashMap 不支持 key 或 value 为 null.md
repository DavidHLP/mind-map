# 为什么 Java 的 ConcurrentHashMap 不支持 key 或 value 为 null？

## 回答重点

- **原因一：避免混淆与潜在并发问题**
  - 在并发环境下，若允许 `null`：
    - `get(key)` 返回 `null` 时，无法区分：
      - 是 `key` 不存在；
      - 还是 `value` 实际为 `null`。
  - 这种歧义会导致逻辑错误，难以维护。
- **原因二：简化实现**

  - 若支持 `null`，需为每个操作（`get`、`put`、`containsKey` 等）额外添加判断逻辑；
  - 增加代码复杂度；
  - 并发修改或删除时，`null` 值可能导致不可预测的状态。

- [x] 因此，JDK 设计者禁止 `ConcurrentHashMap` 中 `key` 或 `value` 为 `null`，以保证语义清晰与线程安全。

---

## 扩展知识

### 其他并发集合的设计一致性

- `CopyOnWriteArrayList`、`ConcurrentSkipListMap` 等并发集合同样 **不允许 null**；
- 原因一致：
  - 防止并发条件下的空值歧义；
  - 确保线程安全和可预期行为。

---

## 源码分析（JDK 1.8）

### put 方法源码

#### 源码

```java
public V put(K key, V value) {
    if (key == null || value == null)
        throw new NullPointerException();
    return putVal(key, value, false);
}
```

#### 源码解读：

1. 首先检查 `key` 与 `value` 是否为 `null`；
2. 若为 `null`，直接抛出 `NullPointerException`；
3. 保证后续操作（如哈希计算、节点创建）不会出错。

---

### get 方法源码

#### 源码

```java
public V get(Object key) {
    Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
    int h = spread(key.hashCode());
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (e = tabAt(tab, (n - 1) & h)) != null) {
        if ((eh = e.hash) == h) {
            if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                return e.val;
        }
        else if (eh < 0)
            return (p = e.find(h, key)) != null ? p.val : null;
        while ((e = e.next) != null) {
            if (e.hash == h &&
                ((ek = e.key) == key || (ek != null && key.equals(ek))))
                return e.val;
        }
    }
    return null;
}
```

#### 源码解读：

- `key` 必须非空，否则无法计算 `hash`；
- 通过 `hash` 定位桶；
- 遍历链表或树结构找到对应节点；
- 返回节点的 `val`；
- 若未找到，返回 `null`；

  - 此处的 `null` 明确表示 **key 不存在**；
  - 而非 **value 为 null**。

- 因为禁止存储 `null` 值，所以 `get` 的返回值语义单一清晰。

---

## 总结

| 维度           | 允许 null 会带来的问题              | 不允许 null 的好处       |
| -------------- | ----------------------------------- | ------------------------ |
| **语义歧义**   | 无法区分 key 不存在或 value 为 null | `null` 仅代表 key 不存在 |
| **并发安全**   | 可能导致并发条件下状态不一致        | 保证一致性与确定性       |
| **代码复杂度** | 需额外判断、维护成本高              | 实现简洁，行为可预测     |
| **性能**       | 增加同步与判空成本                  | 高效读写，读操作无锁     |

---

## 图示：`get` 流程可视化

![ConcurrentHashMap get 流程](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/lctBikgm_image_mianshiya.png)

---

## 延伸：与 HashMap 的对比

| 特性                | HashMap  | ConcurrentHashMap  |
| ------------------- | -------- | ------------------ |
| 线程安全            | ❌ 否    | ✅ 是              |
| 支持 null key/value | ✅ 是    | ❌ 否              |
| 并发机制            | 无       | CAS + synchronized |
| 性能特征            | 单线程快 | 多线程高并发更优   |

---

## 结论

- `ConcurrentHashMap` 不支持 `null` key/value；
- 原因在于：

  - 避免语义混淆；
  - 提升并发安全；
  - 简化内部逻辑；

- 在并发环境中：

  - `get()` 返回的 `null` **唯一表示 key 不存在**；
  - 提高了 API 的一致性与可预测性。

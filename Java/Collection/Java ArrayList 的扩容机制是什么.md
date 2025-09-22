---
markmap:
  colorFreezeLevel: 2
---

# Java ArrayList 的扩容机制是什么

## 扩容触发条件

- 当 ArrayList 中的元素数量超过其当前容量时触发
- 默认初始容量：10

## 扩容过程

- 新数组容量 = 原容量的 1.5 倍
  - 公式：`oldCapacity + (oldCapacity >> 1)`
- 使用 `Arrays.copyOf()` 将元素复制到新数组

## 性能问题

- 扩容耗时：需要分配新内存 + 元素复制
- 频繁扩容影响性能
- 建议：根据预期元素数量设置初始容量
  - 示例：`new ArrayList<>(50);`

## 与负载因子的关系

- ArrayList 扩容机制与负载因子无关
- 不同于 HashMap 等基于负载因子的扩容策略
- 采用固定比例（1.5 倍）扩容

## 源码解析

### add 方法

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // 确保容量
    elementData[size++] = e;
    return true;
}
```

### ensureCapacityInternal

- 调用 `ensureExplicitCapacity(minCapacity)`
- `minCapacity = size + 1`

### ensureExplicitCapacity

```java
if (minCapacity - elementData.length > 0)
    grow(minCapacity);
```

### grow 方法

```java
int oldCapacity = elementData.length;
int newCapacity = oldCapacity + (oldCapacity >> 1); // 1.5倍
if (newCapacity - minCapacity < 0)
    newCapacity = minCapacity;
if (newCapacity - MAX_ARRAY_SIZE > 0)
    newCapacity = hugeCapacity(minCapacity);

elementData = Arrays.copyOf(elementData, newCapacity);
```

### hugeCapacity

- 防止超过最大数组限制：`MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8`
- 返回 `MAX_ARRAY_SIZE` 或 `Integer.MAX_VALUE`

## 总结

- 初始容量默认 10
- 扩容倍数：1.5
- 复制数组：`Arrays.copyOf`
- 上限保护：`hugeCapacity`
- 建议合理设置初始容量避免频繁扩容

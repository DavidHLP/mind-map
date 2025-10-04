---
markmap:
  colorFreezeLevel: 2
---

# Java 中 HashMap 的扩容机制

## 回答重点

- **触发条件: 负载因子 (load factor)**

  - 默认值: 0.75
  - 扩容阈值 = 当前容量 \* 0.75
  - 示例: 初始容量 16, 阈值 12, 第 13 个元素触发

- **扩容操作**

  - 容量扩大为当前容量的**两倍** (16 -> 32 -> 64)

- **核心过程: rehashing**
  - 将所有元素重新分配到新的哈希桶中
  - 根据新容量重新计算存储位置

## 扩展知识

- **rehashing 细节 (Java 1.8 优化)**

  - **核心思想**
    - 数组长度是 2 的 n 次方, 扩容为 2 倍
    - 新旧容量二进制只差一个高位
    - 索引计算: `(数组长度-1) & hash`
  - **新位置判断**
    - 元素的 hash 值某一位是 0 还是 1 决定了新位置
    - 通过 `(hash & oldCapacity) == 0` 来判断
      - `true` -> 索引不变
      - `false` -> 新索引 = 原索引 + oldCapacity
  - **图解**
    - ![rehash-logic](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/MZ5ugCPs_10a4f9fc-9621-4d13-a0ce-c0f7dd012001_mianshiya.png)

- **扩容的性能考量**

  - 耗时操作: 需要遍历所有元素并重新计算位置
  - **优化策略**: 创建时预设合适的初始容量, 减少扩容次数

- **场景题**
  - [752. 1G 大的 HashMap 扩容问题](https://www.mianshiya.com/bank/1795650132375805954/question/1797473188553293825)

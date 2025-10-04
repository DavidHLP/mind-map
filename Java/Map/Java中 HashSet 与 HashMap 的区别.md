---
markmap:
  colorFreezeLevel: 2
---

# Java 中 HashSet 与 HashMap 的区别

## 基本区别

- **HashSet**

  - 基于 `HashMap` 实现
  - 用于存储 **一组唯一元素**
  - 不允许重复元素
  - 元素无序（不保证插入顺序）
  - 实际上操作的是 `HashMap` 的 **key** 部分

- **HashMap**
  - 基于哈希表的数据结构
  - 存储 **键值对 (key-value)**
  - **key 必须唯一**，但 **value 可以重复**
  - 允许一个 `null` 键和多个 `null` 值

---

## 内部实现关系

### HashSet 内部依赖 HashMap

- 构造方法中实际是创建了一个新的 `HashMap`：
  ![HashSet构造函数](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/vT34Br6w_ecf08a80-1505-4e88-b377-7aae4059ae9f_mianshiya.png)

---

### HashSet#add 方法调用 HashMap#put

- `add` 方法的核心实现：
  ![HashSet#add方法](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/nbKHvW4j_6d49b926-45df-4943-a57b-4f8e3b0f3867_mianshiya.png)

- 这里的 `PRESENT` 是一个常量对象，仅用于占位：
  ![PRESENT常量](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/neXpAO3Y_b70e575d-d582-43e1-9af8-3279a456e054_mianshiya.png)

---

## 总结

- `HashSet` 是 `HashMap` 的**封装**，只使用了其中的 **key 部分**
- `HashSet` 的主要操作（如 `add()`、`remove()`）实质上都是调用 `HashMap` 的对应方法
- 这种设计简化了实现，避免重复造轮子

---

## 对比总结表

| 特性          | HashSet                | HashMap                        |
| ------------- | ---------------------- | ------------------------------ |
| 存储结构      | 基于 HashMap           | 基于哈希表                     |
| 存储内容      | 仅存储元素（作为 key） | 存储键值对                     |
| 是否允许重复  | 否（元素唯一）         | key 唯一，value 可重复         |
| 是否允许 null | 允许一个 null 元素     | 允许一个 null 键，多个 null 值 |
| 内部实现      | 通过 HashMap 存储 key  | 独立存储 key-value             |
| 典型用途      | 存储唯一元素集合       | 存储键值对映射关系             |

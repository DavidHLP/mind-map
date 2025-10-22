---
markmap:
  colorFreezeLevel: 2
---

# Java 中的 HashSet 和 HashMap 有什么区别

## HashSet

- 基于 `HashMap` 实现的集合类
- 特点：
  - **不允许重复元素**
  - 用于存储一组 **无序的唯一元素**
- 内部实现：
  - 元素存储在 `HashMap` 的 **键 (key)** 中
  - 值 (value) 使用一个常量对象 `PRESENT`

## HashMap

- 基于哈希表的数据结构
- 存储形式：`key-value` 键值对
- 特点：
  - **键 (key)** 必须唯一
  - **值 (value)** 可以重复

## 扩展知识：HashSet 内部依赖 HashMap

### HashSet 构造

- 内部其实就是 `new HashMap<>()`
- ![构造函数](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/vT34Br6w_ecf08a80-1505-4e88-b377-7aae4059ae9f_mianshiya.png)

### HashSet add 方法

- 实际调用的是 `HashMap.put(key, PRESENT)`
- ![add方法](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/nbKHvW4j_6d49b926-45df-4943-a57b-4f8e3b0f3867_mianshiya.png)

### PRESENT 常量

- 一个普通的 `Object` 实例
- 仅用于占位，没有实际意义
- ![PRESENT](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/neXpAO3Y_b70e575d-d582-43e1-9af8-3279a456e054_mianshiya.png)

### 总结

- `HashSet` 本质上就是对 `HashMap` 的封装
- `HashSet` 的操作（如 `add()`、`remove()` 等）实际上调用的都是 `HashMap` 的方法

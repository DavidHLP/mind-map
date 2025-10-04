---
markmap:
  colorFreezeLevel: 2
---

# Java 中的 IdentityHashMap

## 回答重点

- `Map` 的一种实现
- 使用 **引用相等性 (reference equality)** 作为键的比较方式
- 使用 `==` 比较键值，而不是 `equals()` 方法
- 只有当两个键的引用相同时，才被认为是相同的键

## 使用场景

- **对象身份区分**：需要基于对象身份（引用）进行区分的场景
- **特殊缓存**：确保即使两个对象内容相同，但只要它们是不同的实例，就会被当作不同的键

## 扩展知识

### 主要特性总结

- **引用相等**：使用 `==` 比较键的相等性
- **哈希实现**：不使用 `hashCode()`，而是依赖 `System.identityHashCode()`
- **允许 `null` 键和 `null` 值**
- **非线程安全**

### IdentityHashMap 源码分析

- **`hash` 方法**

  - 使用 `System.identityHashCode(x)` 而不是 `x.hashCode()`
  - <img src="https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203206_mianshiya.png" alt="hash 方法源码" width="500">
  - `System.identityHashCode` 返回默认的 hashCode 实现，忽略重写
  - <img src="https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203221_mianshiya.png" alt="identityHashCode 说明" width="500">
  - 默认实现返回：**对象的内存地址转化成的整数**

- **`get` 方法**

  - 判断 key 是否相等直接用 `==`，即判断地址
  - <img src="https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203229_mianshiya.png" alt="get 方法源码" width="400">

- **结论**

  - `IdentityHashMap` 中的 key 只认对象本身
  - 就算值都相等，只要是不同对象，`put` 进去只会多一个键值对，而不是替换
  - **示例代码** (`identityHashMap` 会存在两个键值对):
    ```java
    Map<String, String> identityHashMap = new IdentityHashMap<>();
    identityHashMap.put(new Yes("1"), "1");
    identityHashMap.put(new Yes("1"), "2");
    ```

- **特殊的存储方式**

  - value 存在 key 的后面 `tab[i+1]`
  - <img src="https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203238_mianshiya.png" alt="存储结构" width="600">

- **适用场景总结**
  - 一种特殊且用途有限的映射实现
  - 主要用于需要引用相等性的场景，如框架中代理对象的映射

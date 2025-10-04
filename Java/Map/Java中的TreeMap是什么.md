---
markmap:
  colorFreezeLevel: 2
---

# Java 中的 TreeMap

## 回答重点

- 内部通过红黑树实现 -可以让 key 实现 `Comparable` 接口或者自定义一个 `Comparator`
- 节点会根据定义的规则进行排序

### 基本特性

- **数据结构**: 基于红黑树实现，保证操作时间复杂度为 O(log n)
- **键的有序性**: 默认按自然顺序排序，也可通过 `Comparator` 自定义排序
- **不允许 null 键**: 但允许值为 null

### 常用场景

- 与加密相关的操作
- 需要根据字母序排序后拼接成字符串

### 简单使用示例

```java
import java.util.Comparator;
import java.util.TreeMap;

public class TreeMapExample {
    public static void main(String[] args) {
        // 使用自然顺序
        TreeMap<Integer, String> treeMap = new TreeMap<>();
        treeMap.put(3, "Three");
        treeMap.put(1, "One");
        treeMap.put(2, "Two");
        System.out.println("TreeMap: " + treeMap);

        // 使用自定义比较器
        TreeMap<Integer, String> reverseTreeMap = new TreeMap<>(Comparator.reverseOrder());
        reverseTreeMap.put(3, "Three");
        reverseTreeMap.put(1, "One");
        reverseTreeMap.put(2, "Two");
        System.out.println("Reverse TreeMap: " + reverseTreeMap);

        // 获取子映射
        System.out.println("SubMap (1, 3): " + treeMap.subMap(1, 3));

        // 获取前缀映射
        System.out.println("HeadMap (2): " + treeMap.headMap(2));

        // 获取后缀映射
        System.out.println("TailMap (2): " + treeMap.tailMap(2));
    }
}
```

## 扩展知识

### 红黑树介绍

- 一种自平衡二叉搜索树（BST）
- 通过引入**颜色**（红色和黑色）标记节点
- 保证最坏情况下的时间复杂度为 O(log n)
- 应用: Java 中的 `TreeMap` 和 `TreeSet`，Linux 内核虚拟内存管理
- <img src="https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/YH7Re2DW_image_mianshiya.png" alt="image.png" width="400">

### 性质

- 节点是**红色**或**黑色**
- 根节点是**黑色**
- 所有叶子节点（NIL 节点）是**黑色**
- **红色**节点的两个子节点都是**黑色**
- 从任一节点到其每个叶子的所有路径都包含相同数目的**黑色**节点

### 操作

- **插入操作**
  - 新节点初始为红色
  - 可能通过**旋转**和**颜色调整**来修复平衡
- **删除操作**
  - 可能导致树不再平衡
  - 通过**调整颜色**和**旋转**操作来恢复
- **旋转操作**
  - **左旋转**：将某个节点的右子树提升为其父节点
  - **右旋转**：将某个节点的左子树提升为其父节点

### 优点

- **平衡性**: 避免退化成链表，保证查找、插入和删除操作的时间复杂度为 O(log n)
- **灵活性**: 相对于 AVL 树，在频繁插入和删除的场景中调整操作较少，性能更好

---
markmap:
  colorFreezeLevel: 2
---

# Java中 ArrayList 和 LinkedList 有什么区别

## 底层数据结构不同

- ArrayList：基于动态数组实现，内存中连续存储
- LinkedList：基于双向链表实现，节点链接存储，不需连续内存

## 性能区别

### ArrayList

- 随机访问速度快：查找元素 O(1)
- 插入、删除慢：中间位置操作需移动元素 O(n)

### LinkedList

- 随机访问慢：查找元素 O(n)
- 插入、删除快：头尾操作 O(1)

## LinkedList 常见接口

### List 接口方法

- add(E e)：末尾添加元素
- add(int index, E element)：指定位置插入
- get(int index)：获取指定位置元素
- set(int index, E element)：替换指定位置元素
- remove(int index)：移除指定位置元素
- indexOf(Object o)：首次出现位置，未找到返回 -1
- lastIndexOf(Object o)：最后出现位置，未找到返回 -1
- size()：返回元素数量

### Deque 接口方法

- addFirst(E e)：头部插入
- addLast(E e)：尾部插入
- removeFirst()：移除并返回第一个元素
- removeLast()：移除并返回最后一个元素
- peekFirst()：获取首元素，不移除，空则 null
- peekLast()：获取尾元素，不移除，空则 null
- push(E e)：元素入栈（头部）
- pop()：元素出栈（头部）

### 其他常用方法

- clear()：清空列表
- toArray()：转数组
- iterator()：迭代器遍历

### 使用示例

```java
LinkedList<String> linkedList = new LinkedList<>();
linkedList.add("A");
linkedList.add("B");
linkedList.addFirst("C"); // C A B
linkedList.addLast("D");  // C A B D

System.out.println(linkedList.get(2)); // 输出: B
linkedList.removeFirst();              // A B D
System.out.println(linkedList.peekLast()); // 输出: D
```

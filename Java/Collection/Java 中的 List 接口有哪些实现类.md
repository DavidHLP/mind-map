---
markmap:
  colorFreezeLevel: 2
---

# List 接口及其实现类

## ArrayList

- 基于动态数组实现
- 特性：
  - 随机访问快 (O(1))
  - 插入和删除（除末尾）慢 (O(n))
- 非线程安全

## LinkedList

- 基于双向链表实现
- 特性：
  - 两端插入删除快 (O(1))
  - 随机访问需要遍历，慢 (O(n))
- 非线程安全

## Vector

- 基于动态数组
- 与 ArrayList 类似
- 特点：
  - 线程安全（方法都加了 synchronized 锁）
  - 性能较低（同步开销大）
- 示例：
  - ![](https://pic.code-nav.cn/mianshiya/question_picture/1800715091727163393/kRYJS7Jv_image_mianshiya.png)

## Stack

- 继承自 Vector
- 特点：LIFO（后进先出）栈结构

## CopyOnWriteArrayList

- 基于动态数组
- 特点：
  - 所有可变操作（add、set 等）会创建新数组
  - 使用写时复制技术
  - 线程安全
  - 适合多线程环境下**读多写少**

## AbstractList

- 抽象类
- 实现了 List 接口的大部分方法
- 提供基本 List 功能
- 可作为自定义 List 实现的基础类

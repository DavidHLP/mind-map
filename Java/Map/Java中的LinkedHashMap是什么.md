---
mindmap:
  initialExpandLevel: 2
---

# Java 中的 LinkedHashMap 是什么?

## 回答重点

- **定义**
  - 继承自 `HashMap`
  - **保留**了键值对的**插入顺序**或**访问顺序**
- **内部实现**
  - 通过维护一个**双向链表**来记录元素的顺序
- **使用场景**
  - **缓存实现**: 用于 LRU (Least Recently Used) 缓存，可根据访问顺序移除最久未使用的元素
  - **数据存储**: 需要保持元素插入顺序的场景

## 扩展知识

- **插入顺序或访问顺序**
  - 构造方法中可以指定 `accessOrder` 参数
    - `true`: 以访问顺序排序
    - `false` (默认): 以插入顺序排序
- **内部实现剖析**
  - **继承关系**: 父类是 `HashMap`，拥有其所有特性
  - **扩展 Entry**: 在 `HashMap.Entry` 基础上增加了 `before` 和 `after` 两个指针
    - ![Entry扩展](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203048_mianshiya.png)
  - **双向链表**: `before` 和 `after` 指针将所有 `Entry` 串成一个双向链表
    - ![双向链表结构](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203058_mianshiya.png)
  - **钩子方法 (Hook Methods)**
    - `HashMap` 预留了几个方法，由 `LinkedHashMap` 实现以维护链表
    - ![钩子方法](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203123_mianshiya.png)
    - **示例**: `afterNodeInsertion` 在 `HashMap.put` 方法中被调用
      - ![HashMap.put中的调用](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203133_mianshiya.png)
    - `LinkedHashMap` 实现该方法来移除最老的节点
      - ![LinkedHashMap的实现](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203142_mianshiya.png)
- **实现 LRU 缓存**

  - **思路**:
    1. 继承 `LinkedHashMap`
    2. 构造时将 `accessOrder` 设置为 `true`
    3. 重写 `removeEldestEntry` 方法
  - **`removeEldestEntry` 方法**:
    - `LinkedHashMap` 默认实现直接返回 `false`
      - ![默认实现](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219203154_mianshiya.png)
    - **重写逻辑**: 当 `size()` 大于最大缓存容量时返回 `true`
      ```java
      protected boolean removeEldestEntry(Entry<K, V> eldest) {
          return this.size() > this.maxCacheSize;
      }
      ```
  - **完整 LRU 代码示例**:

    ```java
    private static final class LRUCache<K, V> extends LinkedHashMap<K, V> {
        private final int maxCacheSize;

        LRUCache(int initialCapacity, int maxCacheSize) {
            super(initialCapacity, 0.75F, true);
            this.maxCacheSize = maxCacheSize;
        }

        protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
            return this.size() > this.maxCacheSize;
        }
    }
    ```

- **手写 LRU 算法 (面试题)**

  - **思路**: 使用 `HashMap` + 自定义双向链表
  - **代码实现**:

    ```java
    public class LRUCache<K,V> {
        class Node<K,V> {
            K key;
            V value;
            Node<K,V> prev, next;
            public Node(){}
            public Node(K key, V value) {
                this.key = key;
                this.value = value;
            }
        }
        private int capacity;
        private HashMap<K,Node> map;
        private Node<K,V> head;
        private Node<K,V> tail;
        public LRUCache(int capacity) {
            this.capacity = capacity;
            map = new HashMap<>(capacity);
            head = new Node<>();
            tail = new Node<>();
            head.next = tail;
            tail.prev = head;
        }

        public V get(K key) {
            Node<K,V> node = map.get(key);
            if (node == null) {
                return null;
            }
            moveNodeToHead(node);
            return node.value;
        }

        public void put(K key, V value) {
            Node<K,V> node = map.get(key);
            if (node == null) {
                if (map.size() >= capacity) {
                    map.remove(tail.prev.key);
                    removeTailNode();
                }
                Node<K,V> newNode = new Node<>(key, value);
                map.put(key, newNode);
                addToHead(newNode);
            } else {
                node.value = value;
                moveNodeToHead(node);
            }
        }

        private void addToHead(Node<K,V> newNode) {
            newNode.prev = head;
            newNode.next = head.next;
            head.next.prev = newNode;
            head.next = newNode;
        }

        private void moveNodeToHead(Node<K,V> node) {
            removeNode(node);
            addToHead(node);
        }

        private void removeNode(Node<K,V> node) {
            node.prev.next = node.next;
            node.next.prev = node.prev;
        }

        private void removeTailNode() {
            removeNode(tail.prev);
        }

        public static void main(String[] args) {
            LRUCache<Integer,Integer> lruCache = new LRUCache<>(3);
            lruCache.put(1,1);
            lruCache.put(2,2);
            lruCache.put(3,3);
            lruCache.get(1);
            lruCache.put(4,4);
            System.out.println(lruCache); // toString 我就没贴了，代码太长了
        }
    }
    ```

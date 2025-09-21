---
markmap:
  colorFreezeLevel: 2
---

# Java 集合类

## Collection 接口

### ![](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219202800_mianshiya.png)

### List

- ArrayList：基于动态数组，查询快，插入删除慢
- LinkedList：基于双向链表，插入删除快，查询慢
- Vector：线程安全的动态数组，开销较大

### Set

- HashSet：基于哈希表，元素无序，不允许重复
- LinkedHashSet：基于链表和哈希表，维护插入顺序，不允许重复
- TreeSet：基于红黑树，元素有序，不允许重复
  ⚠️ 注意：不能简单说 Set 是无序，要看具体实现类

### Queue

- PriorityQueue：基于优先级堆，元素按照自然顺序或比较器排序
- LinkedList：可作队列，支持 FIFO

## Map 接口

- ![](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/20220219202845_mianshiya.png)

- HashMap：基于哈希表，键值对无序，不允许键重复
- LinkedHashMap：基于链表和哈希表，维护插入顺序，不允许键重复
- TreeMap：基于红黑树，键值对有序，不允许键重复
- Hashtable：线程安全，不允许键或值为 null
- ConcurrentHashMap：线程安全，适合高并发，不允许键或值为 null

## 说明

- 面试常见暖场题，回答大致即可，面试官会根据回答继续挖
- 建议结合类图记忆：接口 → 抽象类 → 实现类
  ![](https://pic.code-nav.cn/mianshiya/question_picture/1816450440005341186/pLwh6DJV_image_mianshiya.png)
- 可延展至接口和抽象类的区别

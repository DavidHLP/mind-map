---
markmap:
  colorFreezeLevel: 2
---

# Java 并发 List 对比

## CopyOnWriteArrayList

### 特性

- 线程安全的 List 实现
- 写时复制机制（修改操作会复制并创建新数组 , add , put , remove ）
- 读操作无锁，写操作需要加锁
- 迭代器弱一致性（不会抛 ConcurrentModificationException，可见快照数据）

### 优点

- 高效读性能：读操作无锁，支持高并发读
- 读写不冲突：写时复制确保读操作安全
- 迭代器安全：遍历时不会受并发修改影响

### 缺点

- 写操作开销大：修改时需复制整个数组
- 内存消耗大：写时存在双份数组
- 不适合频繁写入的场景

### 适用场景

- 读多写少
- 白名单、黑名单、配置信息缓存
- 场景对一致性要求不高，但读性能要求高

## Collections.synchronizedList

### 特性

- 包装器：将任意 List 转为线程安全版本
- 对所有访问方法（get、set、add、remove）加 synchronized 锁
- 迭代时需要手动对外部加锁才能保证线程安全

### 优点

- 简单方便：一行代码即可获得线程安全 List
- 适用于已有 List 的快速改造

### 缺点

- 并发性能低：读写操作均需加锁
- 迭代器不安全：遍历时需额外同步
- 在高并发场景下容易成为瓶颈

### 适用场景

- 临时需要线程安全 List
- 并发度不高的场景
- 简单场景下快速实现线程安全
- 更复杂/高并发需求应使用专门的并发集合类（如 CopyOnWriteArrayList、ConcurrentHashMap）

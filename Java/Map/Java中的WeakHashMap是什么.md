# Java 中的 WeakHashMap

## 一、核心概念

- **WeakHashMap** 是 Java 中的一个特殊的 `Map` 实现
- 使用 **弱引用（Weak Reference）** 作为键
- 当键对象没有其他强引用时，**垃圾回收器可回收键及其关联条目**
- 条目被自动移除，避免内存泄漏

## 二、应用场景

- **缓存（Cache）实现**
  - 在内存敏感场景中使用
  - 当键不再被引用时自动清除缓存项，节省内存
- **动态代理缓存**
  - Java 动态代理生成的代理类实例可缓存于 `WeakHashMap`
  - 当代理类不再使用时可自动释放内存
- **Event Listener 管理**
  - 管理事件监听器对象生命周期
  - 当监听器对象不再被引用时自动移除，防止内存泄漏

## 三、引用类型扩展知识

- **强引用（Strong Reference）**
  - 普通引用，GC 永远不会回收
- **软引用（Soft Reference）**
  - 内存不足时会被回收，用于缓存
- **弱引用（Weak Reference）**
  - 一旦没有强引用，GC 会立即回收
- **虚引用（Phantom Reference）**
  - 主要用于追踪对象被回收的时间，用于底层资源清理

## 四、典型使用示例

```java
Map<Object, String> map = new WeakHashMap<>();
Object key = new Object();
map.put(key, "value");

// 当 key 不再被强引用时，GC 后 map 自动清除该条目
key = null;
System.gc();
```

## 五、优点与注意事项

- [x] 自动释放无用条目，防止内存泄漏
- [x] 适用于缓存、监听器、代理等临时性对象
- [x] 不适合存放必须长期存在的数据
- [x] 在高频 GC 环境中，条目可能被意外回收

## 六、参考资料

- [Java 中的强引用、软引用、弱引用和虚引用](https://www.mianshiya.com/question/1780933295211573249)

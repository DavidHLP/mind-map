# ConcurrentModificationException 是什么 & 如何产生

## 回答重点

- 典型触发：**遍历集合时对集合做结构性修改**（add/remove/clear 等）
- 这是 Java 集合的 **fail-fast** 机制：一旦检测到并发修改，**立即抛出异常**，避免语义不一致
- 多线程下使用**非线程安全集合**并发修改，更容易出现该异常

## 扩展知识

### 原理（modCount / expectedModCount）

- 集合内部维护 **modCount**（修改次数）
- 迭代器创建时保存 **expectedModCount = modCount**
- 每次 `hasNext()/next()` 都会校验：若 `modCount != expectedModCount` → 抛出 `ConcurrentModificationException`

### 以 ArrayList 为例分析（源码片段）

```java
public class ArrayList<E> extends AbstractList<E> implements List<E> {
    transient int modCount = 0;

    public Iterator<E> iterator() {
        return new Itr();
    }

    private class Itr implements Iterator<E> {
        private int cursor; // 当前游标位置
        private int expectedModCount = modCount; // 预期的修改次数

        public boolean hasNext() {
            checkForComodification();
            return cursor != size;
        }

        public E next() {
            checkForComodification();
            return ArrayList.this.get(cursor++); // 获取当前元素并移动游标
        }

        final void checkForComodification() {
            if (modCount != expectedModCount) {
                throw new ConcurrentModificationException();
            }
        }
    }
}
```

**源码解读**

- `modCount`：集合修改次数
- `expectedModCount`：迭代器捕获的期望修改次数
- 迭代期间若集合被结构性改动，`modCount` 增加，与 `expectedModCount` 不一致即抛出异常

## 解决方案

- **使用 `Iterator.remove()`**

  - 在迭代过程中删除元素的**唯一安全方式**
  - 注意一次 `next()` 之后只能调用一次 `remove()`，否则会 `IllegalStateException`

- **使用线程安全/并发集合**

  - 如 `ConcurrentHashMap`、`CopyOnWriteArrayList`
  - 这些集合的迭代器通常是 **fail-safe**（基于快照），不会抛 CME，但也可能**看不到最新写入**

- **外部同步控制**

  - 例如 `Collections.synchronizedList(list)` 并在**遍历与修改时使用同一把锁**
  - 或者手动 `synchronized(lock) { ... }` 包住遍历与修改的临界区

- **其他实践**

  - 若需要收集并统一删除：先记录待删元素，遍历结束后再批量修改
  - 使用 `ListIterator` 的 `add/remove/set` 进行边遍历边修改

## Iterator.remove 示例

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class ConcurrentModificationExceptionExample {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");

        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            String s = iterator.next();
            if (s.equals("A")) {
                iterator.remove();  // 正确的删除方式
            }
        }
        System.out.println(list);  // 输出: [B, C]
    }
}
```

## 常见触发场景（便于排查）

- **增强 for/for-each**（底层也是迭代器）中直接调用 `list.remove(obj)`
- **多线程**：一个线程迭代，另一个线程修改非线程安全集合
- **Map 视图**：遍历 `map.keySet()/entrySet()` 时直接对原 map 做结构性修改
- **Stream 迭代**阶段对同一底层集合做结构性写入

## 什么是 Fail-fast？

- **Fail-fast**：一旦检测到不一致/错误，立即失败并抛出异常
- 目的：**尽早暴露问题**，防止错误在后续计算中扩大或导致数据不一致
- 与 **Fail-safe** 对比：后者基于快照或复制（如 `CopyOnWriteArrayList`），迭代期间**不抛异常**，但可能看不到并发修改的最新结果

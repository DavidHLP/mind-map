# hashCode 与 equals 的关系
* 回答重点

  * 在 Java 中，`hashCode()` 和 `equals()` 的关系主要体现在集合类（如 `HashMap`、`HashSet`）中
  * 它俩决定了对象的**逻辑相等性**和**哈希存储方式**
  * **`equals()` 方法**

    * 用于判断两个对象是否相等
    * 默认实现使用 `==` 比较对象的内存地址
    * 可在类中重写以定义自定义相等逻辑
  * **`hashCode()` 方法**

    * 返回对象的哈希值
    * 主要用于基于哈希的集合（如 `HashMap`、`HashSet`）
    * 同一个对象在一次应用执行过程中多次调用 `hashCode()` 必须返回相同的值
    * **相等的对象必须有相同的哈希码**

* 两者的关系

  * 若 `a.equals(b) == true`，则 **`a.hashCode() == b.hashCode()` 必须为 `true`**
  * **反之不要求**：两个对象 `hashCode()` 相同，不一定 `equals()` 相等
  * 若违背上述关系，会导致基于哈希的集合出现错误行为（如 `HashMap` 无法正确存取元素）

* 扩展知识

  * 为什么要重写 `hashCode()` 和 `equals()`

    * `HashMap`、`HashSet` 等集合依赖这两个方法来确定元素的存储与相等性判断
    * 未正确重写可能导致重复存储、查找失败等问题
  * 重写 `equals()` 方法的基本规则

    * 自反性：对任何非空对象 `x`，`x.equals(x)` 为 `true`
    * 对称性：对任何非空对象 `x`、`y`，`x.equals(y)` 与 `y.equals(x)` 结果相同
    * 传递性：`x.equals(y)`、`y.equals(z)` 为 `true` ⇒ `x.equals(z)` 为 `true`
    * 一致性：对象未变更时，多次调用 `x.equals(y)` 结果一致
    * 对于 `null`：对任何非空对象 `x`，`x.equals(null)` 为 `false`
  * 重写 `hashCode()` 方法的基本规则

    * 同一对象在一次应用执行过程中多次调用应返回相同值
    * 若两个对象 `equals()` 相等，则其 `hashCode()` 必须相等
    * 若两个对象 `equals()` 不相等，其 `hashCode()` 不必不同（不同可提升散列表性能）

* hashCode & equals & 集合源码分析

  * `hashCode`：`Object` 的 `native` 方法，返回一个 `int` 哈希码（一般与对象内存地址相关）

    * 示意图：![image.png](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/ag0G48da_image_mianshiya.png)
  * `equals`：`Object` 的方法，默认实现等价于使用 `==` 比较内存地址

    * 示意图：![image.png](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/epOMLaLx_image_mianshiya.png)
  * 二者为何“放在一起说”

    * 在散列表相关类（如 `HashSet`、`HashMap`）中二者**共同参与**元素定位与相等判断
  * 仅重写 `equals` 的问题示例

    * 场景：`HashSet<Yes>`，仅重写 `Yes.equals`（如 `name` 相同返回 `true`）
    * 结果：集合中可能出现相同 `name` 的重复元素（因为 `hashCode` 未重写）
  * `HashSet` 复用 `HashMap` 的存储逻辑

    * 放入 `key` 时先计算 `hash`，实际调用对象的 `hashCode` 来计算

      * 截图：![image-20220123131304189.png](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/image-20220123131304189_mianshiya.png)
    * `putVal` 过程中先比 `hash`，`hash` 相等再比 `equals`；`hash` 不等直接判不相等

      * 截图：![image-20220123131544363.png](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/image-20220123131544363_mianshiya.png)
  * 结论与建议

    * **重写 `equals` 时，务必同时重写 `hashCode`**，确保判断条件一致
    * 即使暂不使用散列表，也建议一起重写，避免潜在问题

* 代码示例（仅重写 `equals` 的示意）

  * ```java
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj instanceof Yes) {
            Yes other = (Yes) obj;
            return name.equals(other.name);
        }
        return false;
    }
    ```

# Java 对象比较：hashCode / equals / ==

* 回答重点

  * `hashCode、equals` 和 `==` 都是 Java 中用于比较对象的三种方式，但用途与实现不同
  * `hashCode`

    * 用于散列存储结构中确定对象的存储位置
    * 可用于快速比较两个对象是否不同（哈希码不同 → 一定不相等）
  * `equals`

    * 比较两个对象的**内容**是否相等
    * 通常需要在自定义类中**重写**以实现自定义比较逻辑
  * `==`

    * 比较两个**引用**是否指向同一个对象（即内存地址）
    * 对于**基本数据类型**，比较它们的值

* 扩展知识

  * hashCode

    * 方法返回对象的哈希码（整数）
    * 主要用于支持基于哈希表的集合（如 `HashMap`、`HashSet`）
    * `Object` 默认实现：基于对象内存地址生成哈希码（`native` 方法）
    * （示意图）![hashCode image](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/ag0G48da_image_mianshiya.png)
    * 与 `equals` 的**合约**：

      * 如果两个对象根据 `equals` 相等，则它们**必须**具有相同的哈希码
      * 如果两个对象具有相同的哈希码，它们**不一定**相等，但会被放在同一个哈希桶中
  * equals

    * 用于比较两个对象的**内容**是否相等
    * `Object` 默认实现：使用 `==` 比较内存地址
    * （示意图）![equals image](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/epOMLaLx_image_mianshiya.png)
    * 通常在自定义类中需要基于属性**重写**`equals`（例如按名字或年龄定义相等）
    * 约束：若 `equals` 返回 `true`，则 `hashCode` 必须返回**相同**的值；反之不要求
    * `equals` 的五个要求

      * 自反性：对任何非空引用 `x`，`x.equals(x)` 返回 `true`
      * 对称性：对任何非空引用 `x`、`y`，若 `x.equals(y)` 为 `true`，则 `y.equals(x)` 也为 `true`
      * 传递性：对任何非空引用 `x`、`y`、`z`，若 `x.equals(y)` 和 `y.equals(z)` 均为 `true`，则 `x.equals(z)` 为 `true`
      * 一致性：对象在比较期间未被修改时，多次调用 `x.equals(y)` 应返回相同结果
      * 对任何非空引用 `x`，`x.equals(null)` 必须返回 `false`
  * ==

    * 比较两个**引用**是否指向同一个对象（比较内存地址）
    * 若为**基本数据类型**，则比较其**值**

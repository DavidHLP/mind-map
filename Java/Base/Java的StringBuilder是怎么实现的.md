# StringBuilder 内部实现

* 回答重点

  * StringBuilder 主要为解决 String 的不可变性，提供高效、动态的字符串拼接与修改（如 `append`、`insert` 等）
  * 核心思路：用可变的数组保存字符序列 + 按需扩容

* 核心实现

  * 底层存储

    * 早期：`char[] value` 存储字符序列
    * 记录长度：`count` 记录已用字符数
  * 直接修改数组

    * `append()`、`insert()` 等直接改动内部数组，不像 `String` 那样创建新对象
  * 扩容策略

    * 容量不足即扩容，典型为 **2 倍 + 2**（`Arrays.copyOf` 拷贝扩容）
    * 目的：减少扩容次数、提升性能
  * 重要提示

    * 数组是**连续内存**结构：下标访问快，但扩容需要新建数组并拷贝
    * 预知大字符串时，应设置初始 `capacity` 以减少扩容

* 与 String 的区别与关联

  * String 同样底层用数组（早期 `char[]`，后续变化见下），但：

    * `String` 为 `final class`
    * 内部数组被 `private final` 修饰，**不可变（Immutable）**
    * 不可变性带来：线程安全、字符串常量池等特性
  * 面试点：为什么 String 不可变而 StringBuilder 可变 → 修饰符与设计目标不同

* 常用方法（面试答题抓重点即可）

  * `append`
  * `insert`
  * `delete`
  * `replace`
  * `charAt`
  * ……

* 关键源码路径（示意）

  * 构造：父类 `AbstractStringBuilder` 分配数组（直接 `new`）
  * 扩容：`ensureCapacityInternal` → `Arrays.copyOf`（2×+2）
  * `append(int)`

    * 步骤：

      * 计算 `int` 转为字符所需长度（`Integer.stringSize`）
      * 判断容量，不足则扩容
      * `Integer.getChars` 将数字倒序写入字符数组
      * 更新 `count`
    * 辅助表：`DigitOnes`、`DigitTens`、`digits` —— **查表法**（高效而直观）
    * 同类：`String.valueOf(int)` 亦依赖 `stringSize`/`getChars`
  * `insert`

    * 将待插入对象（如 `int`）转换为 `String`
    * 判断/扩容 → `System.arraycopy` 移动已有数据腾位 → 写入 → 更新 `count`
  * `delete`

    * 本质为数组删除：计算区间 → `System.arraycopy` 左移覆盖 → 更新 `count`
  * 其它（`replace`、`charAt` 等）

    * 亦为数组区段操作的变形

* 设计与性能要点

  * 充分利用**连续内存**的高局部性与下标访问速度
  * 控制扩容次数：合理初始 `capacity`
  * 原生 `StringBuilder` **无缩容**：删除大量字符后仍可能占用较大内存

* 进一步优化思路（可在面试中加分）

  * 删除时引入**自适应缩容**策略（权衡抖动与碎片）
  * JDK 9+ 的紧凑字符串（Compact Strings）

    * 底层由 `char[]` → **`byte[] + coder` 标志位**
    * 对 Latin-1 等单字节字符更省内存
    * `append` 等方法根据 `coder` 分支处理（兼顾 UTF-16）

* 对比/延伸（配合图示讲解）

  * StringBuilder 与 String 底层示意

    * ![StringBuilder char 数组示意](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/8VJ4iJFj_image_mianshiya.png)
    * ![String 的 final/不可变性](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/0yEQaxjH_d7449805-d279-4fcf-b750-cddcc955fb18_mianshiya.png)
  * 容量/扩容与数组迁移

    * ![容量与扩容](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/8B9x4Wt4_image_mianshiya.png)
    * ![父类构造直配数组](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/TI3WFCMw_image.png)
  * `append` 相关实现与工具方法

    * ![append 多重重载](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/V8a3TbyL_image_mianshiya.png)
    * ![append(int) 关键流程](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/3LCl6bkZ_image_mianshiya.png)
    * ![ensureCapacityInternal 扩容](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/02n5GITK_image_mianshiya.png)
    * ![Integer.stringSize 查表法](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/54mSpRG0_image_mianshiya.png)
    * ![Integer.getChars 写入字符](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/8Tzwc1Pc_image_mianshiya.png)
    * ![DigitOnes / DigitTens 查表](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/pQtnSkHz_image_mianshiya.png)
    * ![String.valueOf(int) 关系](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/REyon8Ql_image_mianshiya.png)
  * `insert` / `delete` 细节

    * ![insert(int) 入口](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/kG3NDIex_image_mianshiya.png)
    * ![insert 核心移动/扩容逻辑](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/WSkvLvre_image_mianshiya.png)
    * ![delete 区段删除](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/1Qm2SWpw_image_mianshiya.png)
  * JDK 11 紧凑字符串示意

    * ![byte\[\] + coder 字段](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/RCFLOn57_image.png)
    * ![append 分支按 coder 处理](https://pic.code-nav.cn/mianshiya/question_picture/1783388929455529986/ygmkjwEp_image_mianshiya.png)

* 面试答题模板（思路复述）

  * 需要实现的关键方法：`append`、`insert`、`delete`、`replace`、`charAt`
  * 底层为**可变数组**，操作前先**校验容量**（必要时扩容），再进行区段移动（`System.arraycopy`）
  * 追加/插入数字类型：先计算位数（`Integer.stringSize`），再写入（`Integer.getChars`）
  * 可加分：说明**无缩容**的现实 → 自主设计“阈值缩容”；说明 **JDK 9+** 的 **`byte[] + coder`** 优化思路

* 总结

  * StringBuilder 的本质就是**数组操作 + 扩容策略**
  * 连续内存带来高效下标访问；动态性带来扩容成本
  * 通过合理 `capacity`、减少扩容、（可选）自适应缩容，获得更好性能

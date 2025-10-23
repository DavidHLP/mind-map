# Java中Exception和Error有什么区别

## 回答重点
- `Exception` 和 `Error` 都是 `Throwable` 类的子类（在 Java 代码中只有继承了 Throwable 类的实例才可以被 throw 或者被 catch），它们表示在程序运行时发生的异常或错误情况。
- 总结：
  - **`Exception`**：可以被处理的程序异常
  - **`Error`**：系统级的不可恢复错误

## 详细说明
### Exception
- 是程序中可以处理的异常情况，表示程序逻辑或外部环境中的问题，可以通过代码进行恢复或处理。
- 常见子类：
  - `IOException`
  - `SQLException`
  - `NullPointerException`
  - `IndexOutOfBoundsException`
- 分类：
  - **Checked Exception（编译期异常）**：编译时必须显式处理（`try-catch` 或 `throws`）。例：`IOException`。
  - **Unchecked Exception（运行时异常）**：不需要显式捕获，继承自 `RuntimeException`。例：`NullPointerException`、`IllegalArgumentException`。

### Error
- 表示严重的错误，通常是 JVM 层次内系统级、无法预料的错误，程序无法通过代码进行处理或恢复。
- 例：
  - `OutOfMemoryError`（内存耗尽）
  - `StackOverflowError`（栈溢出）
- 一般不应该被程序捕获或处理，因为出现这类错误时程序通常无法继续运行。

# 扩展知识
## 异常处理时需要注意的六个点
1. 尽量不要捕获通用异常（如 `Exception`），而应捕获**特定**异常
   - 使代码语义清晰，避免把希望抛出的异常也一并吞掉。

2. 不要“吞”异常
   - 捕获后不抛出、也不记录日志，会导致线上问题难以诊断。
   - 不建议仅用 `e.printStackTrace()`（标准错误流可能丢失），应将详细信息输出到日志系统中，便于排查。
   - 示意图：
     - ![](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/image-20210303201223649.png)

3. 不要延迟处理异常
   - 例如入参 `name` 为 `null`，应尽早校验并抛出清晰错误，而不是在调用一串方法后才抛 `NullPointerException`，导致堆栈冗长、定位困难。

4. `try-catch` 只包裹**必要**的代码段，范围能小则小
   - 大范围 `try-catch` 会影响 JVM 对代码的优化（如指令重排序）。

5. 不要用**异常**来控制程序流程
   - 能用 `if/else` 判断的场景（如 `null` 检查）不要用抛异常替代。
   - 实例化异常会进行栈快照，是相对**昂贵**的操作，频繁使用会有开销。

6. 不要在 `finally` 中处理返回值或直接 `return`
   - 可能覆盖 `try` 中的 `return` 或屏蔽异常，造成诡异行为。

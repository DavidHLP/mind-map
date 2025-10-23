# 什么是Java的多态特性

## 回答重点
- 多态是指同一个接口或父类引用变量可以指向不同的对象实例，并根据实际指向的对象类型执行相应的方法。
- 它允许同一方法在不同对象上表现出不同的行为，是面向对象编程（OOP）的核心特性之一。
- **多态的优点**
  - 通过多态，程序可以灵活地处理不同类型的对象，降低代码耦合度，增强系统的可扩展性。
  - 新增子类或实现类时，无需修改原有代码，只需通过接口或父类引用调用即可。

## 扩展知识
### 多态的意义（理解版）
- 多态是一种抽象行为，让程序员可以**面向抽象编程**而不是具体实现类，从而提升扩展性。
- 类比
  - “水果”是抽象，“苹果/桃子”是具体实现。
  - 如果从“喜欢水果”切换到“喜欢桃子”，抽象层写法无需修改；具体层写法需要替换。
- 代码例子
  - `Person person = new Student()`
  - `Person` 为父类，含有 `work()` 方法；`Student` 重写 `work()`（如“上学”）。
  - 演示
    ```java
    class Person {
       void work() {
           System.out.println("工作");
       }
    }

    class Student extends Person {
       @Override
       void work() {
           System.out.println("上学");
       }
    }

    public class Test {
       public static void main(String[] args) {
           Person person = new Student();
           person.work(); // 输出 "上学"
       }
    }
    ```
- 结论
  - 使用时对象都是 `person`，但 `new` 不同实现类，表现不同——这就是“多态”。

### 编译时多态和运行时多态
- 定义
  - **编译时多态**：方法**重载**，在**编译时**确定调用目标。
  - **运行时多态**：方法**重写**，在**运行时**根据实际对象类型动态绑定。

#### 1. 编译时多态（Compile-time Polymorphism / 静态多态）
- 实现方式：**方法重载（Method Overloading）**
  - 同一类中多个同名方法，参数列表（类型或数量）不同。
  - 编译器依据实参的类型与数量在编译阶段决定调哪个方法。
- 示例
  ```java
  class Example {
      void display(int a) {
          System.out.println("Integer: " + a);
      }

      void display(double a) {
          System.out.println("Double: " + a);
      }

      void display(String a) {
          System.out.println("String: " + a);
      }
  }

  public class Main {
      public static void main(String[] args) {
          Example obj = new Example();
          obj.display(5);         // 调用 display(int a)
          obj.display(3.14);      // 调用 display(double a)
          obj.display("Hello");   // 调用 display(String a)
      }
  }

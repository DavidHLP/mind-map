# Java方法重载和方法重写之间的区别是什么
## 回答重点
- **方法重载（Overloading）**：在同一个类中，允许有多个同名方法，只要参数列表不同（参数个数、类型或顺序）。关注方法的**签名**变化，适用于同一类中不同场景的行为。
- **方法重写（Overriding）**：子类在继承父类时，**参数列表与方法名必须相同**，为父类方法提供新的实现。关注**继承关系**，用于子类改变父类实现，实现**运行时多态**。

## 区别主要如下
- 发生的场所
  - 重载：在同一个类中
  - 重写：在继承关系的子类和父类之间
- 参数列表
  - 重载：必须不同（数量、类型或顺序不同）
  - 重写：必须相同，不能改变参数列表
- 返回类型
  - 重载：可以不同
  - 重写：必须与父类方法返回类型相同，或为其子类型（**协变返回类型**）
- 访问修饰符
  - 重载：不受访问修饰符影响
  - 重写：子类方法的访问级别**不能更严格**，应相同或更宽泛
- 静态与非静态
  - 重载：可为静态或非静态
  - 重写：**只能**重写非静态；静态方法**不能被重写**（只能**隐藏**）
- 异常处理
  - 重载：异常声明可不同
  - 重写：子类抛出的异常**不得多于**父类（可更少或更具体的子类型）

## 扩展知识

### 重载

#### 重载注意点
- 方法同名但**参数列表不同**（数量、类型或顺序）；**与返回值无关**（返回值不构成签名）。
- 常用于：同一操作的不同实现（如**构造函数重载**、不同类型输入的处理）。

#### 重载示例
```java
public class OverloadingExample {
    // 参数数量不同
    public void print(int a) {
        System.out.println("Printing int: " + a);
    }
    // 参数类型不同
    public void print(String a) {
        System.out.println("Printing String: " + a);
    }
    // 参数类型与数量不同
    public void print(int a, int b) {
        System.out.println("Printing two ints: " + a + ", " + b);
    }
}
```

### 重写

#### 重写注意点

-  子类方法**访问级别**不可更严格（如父类 `protected`，子类不可改为 `private`，但可改为 `public`）。
-  子类方法的**异常**需与父类一致或为其子类型。
-  使用 **`@Override`** 注解可由编译器校验是否正确重写，避免误写。

#### 重写示例

```java
class Parent {
    public void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    @Override
    public void display() {
        System.out.println("Child display");
    }
}

public class OverridingExample {
    public static void main(String[] args) {
        Parent obj = new Child();
        obj.display(); // 输出 "Child display"
    }
}
```
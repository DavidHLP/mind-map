# Java 内部类

* 回答重点

  * **Java 内部类** 是指在一个类的内部定义的类，Java 支持多种类型的内部类，包括成员内部类、局部内部类、匿名内部类和静态内部类。内部类可以访问外部类的成员变量和方法，甚至包括私有的成员。
  * **内部类的作用** 主要包括：

    1. **封装性**：将逻辑相关的类封装在一起，提高类的内聚性。
    2. **访问外部类成员**：内部类可以方便地访问外部类的成员变量和方法，尤其在需要操作外部类对象的场景下非常有用。
    3. **简化代码**：对于只在一个地方使用的小类，内部类能减少冗余代码，简化结构。
    4. **事件处理**：匿名内部类广泛用于实现回调函数或事件监听，简化了代码结构，特别是对于实现接口或抽象类的场景。

* 扩展知识

  * 内部类的类型

    * **成员内部类**：非静态类，作为外部类的一个成员。它可以直接访问外部类的所有成员，包括私有成员。
    * **静态内部类**：定义为 `static`，无法访问外部类的非静态成员，只能访问外部类的静态成员。
    * **局部内部类**：定义在方法或代码块中的类，仅在该方法或代码块内可见，通常用于临时的对象构建。
    * **匿名内部类**：没有类名的内部类，通常用于创建短期使用的类实例，尤其是在接口回调或事件处理时被广泛使用。
  * 内部类举例

    * 1）**成员内部类**，定义在另一个类中的类，可以使用外部类的所有成员变量以及方法，包括 private 的。

      * 代码示例

        ```java
        public class OuterClass {
            private String outerField = "Outer Field";

            class InnerClass {
                void display() {
                    System.out.println("Outer Field: " + outerField);
                }
            }

            public void createInner() {
                InnerClass inner = new InnerClass();
                inner.display();
            }
        }
        ```
    * 2）**静态内部类**，只能访问外部类的静态成员变量以及方法，其实它就等于一个顶级类，可以独立于外部类使用，所以更多的只是表明类结构和命名空间。

      * 代码示例

        ```java
        public class OuterClass {
            private static String staticOuterField = "Static Outer Field";

            static class StaticInnerClass {
                void display() {
                    System.out.println("Static Outer Field: " + staticOuterField);
                }
            }

            public static void createStaticInner() {
                StaticInnerClass staticInner = new StaticInnerClass();
                staticInner.display();
            }
        }
        ```
    * 3）**局部内部类**，指在方法中定义的类，只在该方法内可见，可以访问外部类的成员以及方法中的局部变量（需要声明为 final 或 effectively final）。

      * 代码示例

        ```java
        public class OuterClass {
            void outerMethod() {
                final String localVar = "Local Variable";

                class LocalInnerClass {
                    void display() {
                        System.out.println("Local Variable: " + localVar);
                    }
                }

                LocalInnerClass localInner = new LocalInnerClass();
                localInner.display();
            }
        }
        ```
    * 4）**匿名类**，指的是没有类名的内部类。用于简化实现接口和继承类的代码，仅在创建对象时使用，例如回调逻辑定义场景。

      * 代码示例

        ```java
        public class OuterClass {
            interface Greeting {
                void greet();
            }

            public void sayHello() {
                Greeting greeting = new Greeting() {
                    @Override
                    public void greet() {
                        System.out.println("Hello, World!");
                    }
                };
                greeting.greet();
            }
        }
        ```
  * 其他说明

    * 局部内部类用的比较少，常用成员内部类、静态内部类和匿名内部类。
    * 实际上内部类是一个编译层面的概念，像一个语法糖一样，经过编译器之后其实内部类会提升为外部顶级类，和外部类没有任何区别，所以**在 JVM 中是没有内部类的概念的**。

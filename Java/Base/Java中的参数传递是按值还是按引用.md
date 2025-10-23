# Java中的参数传递是按值还是按引用

## 回答重点
- 在 Java 中，**参数传递只有按值传递，不论是基本类型还是引用类型。**
- 基本数据类型（如 `int`, `char`, `boolean` 等）
  - 传递的是值的副本，即基本类型的数值本身
  - 对方法参数的任何修改都不会影响原始变量
- 引用数据类型（如对象引用）
  - 传递的是引用的副本，即对象引用的内存地址
  - 方法内可以通过引用修改对象的属性
  - 但不能改变引用本身，使其指向另一个对象

## 扩展知识
### 基本类型与引用类型的区别
- 基本类型
  - 包括 `int`, `float`, `double`, `char`, `boolean` 等
  - 存储在栈内存中
  - 方法中对基本类型参数的操作只会影响传递的副本，原始变量的值不受影响
- 引用类型
  - 包括所有的对象和数组
  - 变量存储的是对象在堆内存中的地址
  - 当引用类型作为参数传递时，传递的是这个地址的副本
  - **方法内的修改可以影响到传入的对象的内容**，但不会影响对象引用本身的地址
- 示意图
  - ![参数传递示意](https://pic.code-nav.cn/mianshiya/question_picture/1772087337535152129/4VszJ2DN_410280b2-919d-4e4d-953e-fd8350f95a99_mianshiya.png)

### 示例代码分析
- 代码
  - ```java
    public class ParameterPassing {
        public static void main(String[] args) {
            int a = 5;
            modifyPrimitive(a);
            System.out.println("After modifyPrimitive: " + a); // 输出: 5

            MyObject obj = new MyObject();
            obj.value = 10;
            modifyObject(obj);
            System.out.println("After modifyObject: " + obj.value); // 输出: 20

            resetReference(obj);
            System.out.println("After resetReference: " + obj.value); // 输出: 20
        }

        public static void modifyPrimitive(int num) {
            num = 10; // 仅仅修改了副本，不影响原始变量
        }

        public static void modifyObject(MyObject obj) {
            obj.value = 20; // 修改了对象的属性，会影响原始对象
        }

        public static void resetReference(MyObject obj) {
            obj = new MyObject(); // 修改的是引用的副本，不影响原始对象
            obj.value = 30;
        }
    }

    class MyObject {
        int value;
    }
    ```
- 说明
  - `modifyPrimitive`：`num` 是基本类型的副本，修改不影响原始变量 `a`
  - `modifyObject`：`obj` 是引用的副本，但指向原始对象，修改 `value` 影响原始对象
  - `resetReference`：`obj` 被重新赋值为新对象，仅影响**副本**引用，不影响原始引用

### 不可变类
- 面试延伸
  - 不可变类在多线程环境中不需要额外的同步控制，因为它们的状态一旦创建就不能改变
  - 参考：<https://www.mianshiya.com/question/1780933294456598529>

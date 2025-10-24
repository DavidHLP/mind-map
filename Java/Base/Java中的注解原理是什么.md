# Java 注解

* 回答重点

  * **注解其实就是一个标记**，是一种提供元数据的机制，用于给代码添加说明信息。可以标记在类上、方法上、属性上等，标记自身也可以设置一些值。
  * 注解本身不影响程序的逻辑执行，但可以通过工具或框架来利用这些信息进行特定的处理，如代码生成、编译时检查、运行时处理等。

* 扩展知识

  * 注解的使用

    * **定义注解**：注解是一种特殊的接口，以 `@interface` 关键字

      ```java
      @interface MyAnnotation {
       String value() default ""; //可以在注解中为属性指定默认值
      }
      ```
    * **使用注解**：在类、方法、字段等代码元素上。

      ```java
      @MyAnnotation(value = "example")
      public class MyClass {
       @MyAnnotation
       public void myMethod() {}
      }
      ```
    * **处理注解**：

      * **编译时处理**：使用 `javax.annotation.processing` 包进行注解处理器的开发。
      * **运行时处理**：使用反射机制访问注解，通过 `Class.getAnnotation()` 或 `Field.getAnnotation()` 等方法获取注解信息。
      * **示例代码（运行时处理）**：

        ```java
        @Retention(RetentionPolicy.RUNTIME)
        @Target(ElementType.METHOD)
        public @interface MyAnnotation {
          String value();
        }

        public class AnnotationProcessor {
          public static void main(String[] args) throws Exception {
              Method method = MyClass.class.getMethod("myMethod");
              if (method.isAnnotationPresent(MyAnnotation.class)) {
                  MyAnnotation annotation = method.getAnnotation(MyAnnotation.class);
                  System.out.println("Annotation value: " + annotation.value());
              }
          }
        }
        ```
  * 元注解

    * **@Retention**：定义注解的保留策略，即注解的有效范围。

      * `RetentionPolicy.SOURCE`：注解仅在源码中存在，编译时被丢弃。
      * `RetentionPolicy.CLASS`：注解存在于编译后的 `.class` 文件中，但运行时不可用。
      * `RetentionPolicy.RUNTIME`：注解在运行时可用，可以通过反射机制访问。
    * **@Target**：指定注解可以应用于哪些代码元素。

      * `ElementType.TYPE`：类、接口（包括注解类型）或枚举。
      * `ElementType.FIELD`：字段（包括枚举常量）。
      * `ElementType.METHOD`：方法。
      * `ElementType.PARAMETER`：方法参数。
      * `ElementType.CONSTRUCTOR`：构造方法。
      * `ElementType.LOCAL_VARIABLE`：局部变量。
      * `ElementType.ANNOTATION_TYPE`：注解类型。
      * `ElementType.PACKAGE`：包。

* 常见注解例子

  * `@Override`

    * 是给编译器用的，编译器编译的时候检查没问题就 ok 了，class 文件里面不会有 Override 这个标记。
    * 示意图：![image-20210228112243058.png](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/image-20210228112243058.png)
  * `@Autowired`（Spring）

    * 是 RUNTIME 的。所以**在运行的时候可以通过反射得到注解的信息**，还能拿到标记的值 `required`。
    * 示意图：![image-20210228112312107.png](https://pic.code-nav.cn/mianshiya/question_picture/1783397053004488705/image-20210228112312107_mianshiya.png)
  * 结论

    * 所以注解就是一个标记，可以给编译器用、也能运行时候用。

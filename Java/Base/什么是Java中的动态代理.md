# Java 动态代理

* 回答重点

  * 动态代理：**在运行时**创建代理对象的机制

    * 允许在运行时决定代理对象的行为，而非编译时确定
    * 通过代理模式在不修改目标对象的情况下增强/调整其行为
  * 代理作用：作为调用目标的包装

    * 在调用真实目标之前进行逻辑处理，消除重复代码
  * 静态代理 vs 动态代理

    * 静态代理：预先编码好代理类
    * 动态代理：**运行时生成**代理类

* 扩展知识

  * 动态代理主要用途

    * 简化代码：减少横切关注点（日志、事务、权限）中的重复代码
    * 增强灵活性：运行时生成代理对象，可动态改变行为
    * 实现 AOP：在方法调用前后插入额外逻辑
  * **Java 动态代理 与 CGLIB 代理**

    * Java 动态代理：**只能对接口**进行代理（不支持对类）
    * CGLIB 代理：通过字节码技术生成目标类的**子类**进行代理，支持对类（非接口）代理

* JDK 示例代码

  * ```java
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;

    // 目标接口
    interface MyService {
       void doSomething();
    }

    // 目标对象的实现
    class MyServiceImpl implements MyService {
       @Override
       public void doSomething() {
           System.out.println("Doing something...");
       }
    }

    // 动态代理处理器
    class MyInvocationHandler implements InvocationHandler {
       private final Object target;

       public MyInvocationHandler(Object target) {
           this.target = target;
       }

       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           System.out.println("Before method call");
           Object result = method.invoke(target, args);
           System.out.println("After method call");
           return result;
       }
    }

    // 使用动态代理
    public class Main {
       public static void main(String[] args) {
           MyService target = new MyServiceImpl();
           MyService proxy = (MyService) Proxy.newProxyInstance(
               target.getClass().getClassLoader(),
               target.getClass().getInterfaces(),
               new MyInvocationHandler(target)
           );

           proxy.doSomething();
       }
    }
    ```

* CGLIB 示例代码

  * ```java
    import net.sf.cglib.proxy.Enhancer;
    import net.sf.cglib.proxy.MethodInterceptor;
    import net.sf.cglib.proxy.MethodProxy;

    // 目标类
    class MyService {
       public void doSomething() {
           System.out.println("Doing something...");
       }
    }

    // CGLIB 代理处理器
    class MyInterceptor implements MethodInterceptor {
       @Override
       public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
           System.out.println("Before method call");
           Object result = proxy.invokeSuper(obj, args);
           System.out.println("After method call");
           return result;
       }
    }

    // 使用 CGLIB 动态代理
    public class Main {
       public static void main(String[] args) {
           Enhancer enhancer = new Enhancer();
           enhancer.setSuperclass(MyService.class);
           enhancer.setCallback(new MyInterceptor());

           MyService proxy = (MyService) enhancer.create();
           proxy.doSomething();
       }
    }
    ```

* 参考

  * [430. JDK 动态代理和 CGLIB 动态代理有什么区别？](https://www.mianshiya.com/bank/1787463103423897602/question/1780933294599204866)

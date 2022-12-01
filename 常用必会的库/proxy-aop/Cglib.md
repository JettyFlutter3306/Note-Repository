# CGlib

# 快速使用

## 简介

CGlib 是一款用于动态代理的 Java 类库，和 JDK 内置的 Proxy 类针对接口代理不同，CGlib 是针对类的代理，一个不需要实现一个接口也可以实现动态代理。

CGlib 底层基于字节码插装技术，直接编译时增强被代理类，而不是像 Proxy 类是运行时增强。

## 示例

引入 CGlib 依赖：

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
```

编写单元测试：

```java
public class TestCglib {

    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @Accessors(chain = true)
    public static class Person {
        private String name;

        public void eat() {
            System.out.println(name + "正在吃饭");
        }
    }

    @Test
    public void testEnhance() {
        Person person = new Person("洛必达");
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(person.getClass());
        enhancer.setCallback((MethodInterceptor) (o, method, objects, methodProxy) -> {
            Object result;
            if (method.getName().equals("eat")) {
                System.out.println("开始吃饭");
                result = methodProxy.invokeSuper(o, objects);
                System.out.println("结束吃饭");
            } else {
                result = methodProxy.invokeSuper(o, objects);
            }
            return result;
        });
        Person personProxy = (Person) enhancer.create();
        personProxy.eat();
    }
}
```

`Person` 类是被代理的类，`Enhancer` 类是用于创建代理对象的类。*setSuperClass* 方法参数传入被代理对象的字节码对象。*setCallback* 方法传入 `Callback` 接口类型，这里使用的是它的子接口 `MethodInterceptor` 所以要强转。

四个参数：

- *o*：指的是被代理的对象本身。
- *method*：指的是被代理对象的方法。
- *objects*：指的是被代理对象的方法的参数列表。
- *methodProxy*：指的是代理对象。

在 lambda 函数体中要针对对应的方法增强，比如说对 *eat* 方法增强，其他的方法就正常运行即可。要增强的方法在它运行的前后添加一些自定义逻辑就行了。

最后一个 *create* 方法是创建代理对象，但是返回值 `Object` 类型，所以需要强转为 `Person`，然后调用被增强的方法即可。
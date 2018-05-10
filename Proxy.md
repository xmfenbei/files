# Proxy 代理

```
InvocationHandler handler = new MyInvocationHandler(...); kdjfkaljfkldjfkaldjfkldjfkdljfkld
     Foo f = (Foo) Proxy.newProxyInstance(Foo.class.getClassLoader(),
                                          new Class<?>[] { Foo.class },
                                          handler);
```

#### 参考资料：
[Class Proxy](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html)
[轻松学，Java 中的代理模式及动态代理](https://blog.csdn.net/briblue/article/details/73928350)

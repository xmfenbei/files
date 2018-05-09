# java aspectj

### @Aspect
```
@Aspect
@Component
public class DistributedLockAspect {
}
```
### @Pointcut
```
@Pointcut("@annotation(com.xxx.DistributedLock)")
public void distributedLockAspect() {

}
```
### @Before @After @Around
```
@Around(value = "distributedLockAspect()")
public Object doAround(ProceedingJoinPoint pjp) throws Throwable {

}
```
### @AfterReturning
### @AfterThrowing

#### 参考资料：
[8. Aspect Oriented Programming with Spring](https://docs.spring.io/spring/docs/4.0.x/spring-framework-reference/html/aop.html)

[Spring AOP + @AspectJ Annotation Example with @Aspect, @Pointcut, @Before, @After, @Around, @AfterReturning, @AfterThrowing Advice](https://www.concretepage.com/spring/spring-aop-aspectj-annotation-example-with-aspect-pointcut-before-after-around-afterreturning-afterthrowing-advice#around)

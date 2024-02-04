---
title: 基于redisson的串行化请求切面
description: 基于redisson的串行化请求切面
published: true
date: 2024-02-04T08:24:17.450Z
tags: coding, development
editor: markdown
dateCreated: 2024-02-04T08:13:46.139Z
---

# 基于redisson的串行化请求切面

应用场景
1. 某些方法/请求需要串行化执行。比如针对某个资源(如用户)，不能被同时编辑
2. 支持动态设置切点
3. 支持锁住某个方法，以及方法中的某个参数


## 编码实现

### 自定义注解
> 方便要串行化处理的地方传入一些定制化参数
```java
/**
 * 描述: 串行化请求注解
 * @author Vic.xu
 * @since 2024-02-02 10:53
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Documented
@Inherited
public @interface SerialRequest {

    /**
     * 锁名称(前缀：如表名/业务实体名)
     */
    String name();

    /**
     * 锁住的参数：对应方法中的参数,通过spel表达式取值
     * 如:
     *    获取参数id:   #id
     *    获取对象参数user中的id： #user.id
     *    获取map参数userMap中的id： #userMap['id']
     * ★ 不写此参数会锁住整个方法
     */
    String lockParameter() default "";

    /**
     * 获取锁的等待时间(秒) 默认10s, 10s后未能获取到锁，则直接抛出异常，
     */
    long waitTimeSeconds() default 10L;
}

```

### 切面处理类 SerialRequestAdvice
1. 此切面可以看成是在方法开始前加锁，方法结束后解锁，所以是一个环绕通知，所以实现`org.aopalliance.intercept.MethodInterceptor`接口。
   - 前置通知：`org.springframework.aop.MethodBeforeAdvice`
   - 后置通知：`org.springframework.aop.AfterReturningAdvice`
   - 异常通知：`org.springframework.aop.ThrowsAdvice`
2. 通过自定义注解，以及spring的SPEL获取方法参数中对应参数的值
		- 注意此处需要获取编译后的方法参数名称，需要需要注意配置`maven-compiler-plugin`开启此特性(springboot中应该默认启用了此特性)
    ```xml
		<plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
        <compilerArgs>
            <arg>-parameters</arg>
        </compilerArgs>
      </configuration>
   </plugin>
    ```
```java
/**
 * 描述: 通过分布式锁串行化请求切面，根据请求中的业务标识添加锁
 * 如操作用户时候，针对同一个用户id，同一时刻只能被一个请求操作
 * @author Vic.xu
 * @since 2024-02-02 10:28
 */
public class SerialRequestAdvice implements MethodInterceptor {

    private static final Logger LOGGER = LoggerFactory.getLogger(SerialRequestAdvice.class);

    private final SpelExpressionParser spelParser = new SpelExpressionParser();

    private final RedissonClient redissonClient;

    public SerialRequestAdvice(RedissonClient redissonClient) {
        this.redissonClient = redissonClient;
    }

    /**
     * 串行化切面的处理：加锁和释放锁
     * 通过redisson锁住资源，直到业务处理完毕后才释放
     */
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        SerialInfo serialInfo = initSerialInfo(invocation);
        String lockName = serialInfo.lockName;
        RLock lock = redissonClient.getLock(lockName);
        try {
            //此处不设置加锁时间， 通过看门狗默认一直延期锁的生命周期，直到业务流程处理完毕
            boolean obtained = lock.tryLock(serialInfo.waitTime, TimeUnit.SECONDS);
            if (!obtained) {
                throw new RuntimeException("当前资源[" + lockName + "]正在被其他人操作，请稍后再试!");
            }
            LOGGER.info("SerialRequest 锁定 {}", lockName);
            return invocation.proceed();
        } finally {
            if (lock != null && lock.isHeldByCurrentThread()) {
                lock.unlock();
                LOGGER.info("SerialRequest 释放 {}", lockName);
            }

        }
    }

    /**
     * 初始化加锁的相关信息
     */
    private SerialInfo initSerialInfo(MethodInvocation invocation) {
        Method method = invocation.getMethod();
        SerialRequest serialRequest = method.getAnnotation(SerialRequest.class);

        String lockParameter = serialRequest.lockParameter();
        Parameter[] parameters = method.getParameters();
        String lockParameterValue = null;

        if (!ObjectUtils.isEmpty(parameters) && StringUtils.hasLength(lockParameter)) {
            //将方法的参数名和参数值一一对应的放入上下文中
            EvaluationContext ctx = new StandardEvaluationContext();
            Object[] arguments = invocation.getArguments();
            for (int i = 0; i < parameters.length; i++) {
                String name = parameters[i].getName();
                Object argument = arguments[i];
                ctx.setVariable(name, argument);
            }
            lockParameterValue = String.valueOf(spelParser.parseExpression(lockParameter).getValue(ctx));
        }
        return new SerialInfo(serialRequest, lockParameterValue);
    }

    /**
     * 串行化的相关数据
     */
    static class SerialInfo {

        String lockName;

        long waitTime;

        SerialInfo(SerialRequest serialRequest, String lockParameterValue) {
            if (!StringUtils.hasLength(lockParameterValue)) {
                lockParameterValue = "none";
            }
            this.lockName = "sr:" + serialRequest.name() + ":" + lockParameterValue;
            this.waitTime = serialRequest.waitTimeSeconds();
        }
    }

}

---
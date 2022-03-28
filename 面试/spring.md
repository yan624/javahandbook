## 惊人！Spring5 AOP 默认使用Cglib? 从现象到源码深度分析 
[惊人！Spring5 AOP 默认使用Cglib? 从现象到源码深度分析](https://www.cnblogs.com/coderxiaohei/p/11758239.html)

## Spirng是什么？
官方的定义是，Spring 为企业级应用提供了全面的模型，包括 IoC、AOP、Spring MVC、Spring Testing，还有一些关于任务、缓存、邮件之类的集成。

Spring IoC 和 Spring AOP 被他们叫做核心技术。

## 什么是依赖注入？
依赖注入也叫控制反转，指的是将创建对象的控制权交给别人而不是自己，一般是交给 IoC 容器。

依赖注入指的是将对象与对象之间的依赖关系交给 IoC 容器处理。

## 有哪些方法可以实现注入对象？
1. 通过 set 方法
2. 通过 构造器
3. 通过属性直接注入

## IoC有什么优点？
[为什么要用 spring 的 IOC 和 AOP? 用了 IOC 和 AOP 的优点缺点是什么](https://www.zhihu.com/question/19993030/answer/1712944965)

IoC 可以实现资源复用。IoC 在为类创建一个实例对象后，不需要再重复创建。许多对象其实只要创建一次就够了。

IoC 还可以降低耦合度。在需要修改类的代码之后，对象的创建以及依赖关系完全由 IoC 容器维护。

## bean有哪些作用范围？
主要分为单例和多例。还有一些特殊的，比如 request、session、application和websocket。

单例指的是对于某个 bean 定义 一个 IoC 容器中只存在单份对象。

多例指的是可以存在多份对象，使用一次就会创建一个。

后面几个都是指对象处于特定的作用范围中。

request：每个 request 内都会创建一个 bean。在 request 的生命周期内只有单个。

session：每个 session 内都会创建一个 bean。在 session 的生命周期内只有单个。

application：在 ServletContext 的生命周期内只有单个。

websocket：在 WebSocket 的生命周期内只有单个。

## 讲讲bean的生命周期
参考 spring 源码以及 spring core 的官方文档。

bean的完整生命周期很长，有差不多20个步骤。

Spring IoC 会扫描所有 bean 的定义，然后创建它们的实例对象。

接下来，首先会调用所有 Aware 接口的接口方法，然后会调用 `BeanPostProcessor` 的接口方法，在 bean 初始化前调用一次，在之后也会调用一次。

其中 bean 的初始化方法有三种实现方式，分别是 JSR-250 提供的注解（java 注解 `@PostConstruct`），Spring 提供的函数式接口（`InitializingBean` 接口）以及 xml 里自定义的初始化方法。如果一个对象同时配备了这三种方式，那就按一定顺序调用。首先是 JSR-250 的方法，然后是 Spring 的函数式接口，最后是 xml 中配置的方法。

当 bean 销毁时，也有三种方式。调用的顺序是一样。

## bean初始化的具体过程？
BeanDefinition，大致就是上一节的过程。

## bean在什么时候销毁？
默认是项目关闭就销毁了

## Spring是怎么解决循环依赖的？

## 谈谈对aop的理解？
[什么是面向切面编程AOP？](https://www.zhihu.com/question/24863332/answer/350410712)

[为什么要用 spring 的 IOC 和 AOP? 用了 IOC 和 AOP 的优点缺点是什么](https://www.zhihu.com/question/19993030/answer/1712944965)

AOP 指的是面向切面编程。它是 OOP 的一种补充，也是一种编程范式。

它可以在不改变原有代码的情况下执行一些额外的代码。

Spring 中的 aop 实现主要集中在事务、日志记录和异常处理上。

继续介绍切入点、切面、。。。。

## pointcut，joinpoint，advice
[如何用通俗易懂方式解释面向（切面）AOP编程 和AOP的实现方式？](https://www.zhihu.com/question/318501676/answer/1606839429)

切入点就是声明一些感兴趣的连接点，然后就能让我们控制什么时候运行 advice。

连接点就是控制在发生什么事的时候运行 adivce，spring aop 只支持方法执行这一种连接点。

advice 决定在什么时候注入一段代码，比如说 `@After` 就是控制在方法执行完后运行 advice，`@Around` 就是环绕通知。

## 拦截器是什么？和aop有什么区别？
拦截器是 spring 的实现。

拦截器拦截的是 http 请求对应的 java 方法；aop 拦截的是任意的 java 方法。

## springmvc的运行流程？
浏览器向服务器发送 HTTP 请求，这会被 springmvc 的 DispatcherServlet 捕获。

接下来，DispatcherServlet 使用 FlashMapManager 获得与当前请求匹配的 FlashMap。FlashMap 存储的是键值对，它的作用范围比 request 大一点，但是比 session 小很多，主要用于存储两次请求之间的数据。

然后会判断当前请求是否是 MultiPart 请求，如果是的话，就将 request 转为 MultiPart 的 request，用于处理后续的文件上传。

接下里开始处理具体的请求，需要用到处理器映射器（HandlerMapping）和处理器适配器（HandlerAdapter）。

处理器映射器返回相应的处理器执行链，其中含了处理器和拦截器，处理器指的是被 @RequestMapping 标注的方法。

处理器适配器用于适配不同的 mvc 框架，springmvc 有一个默认实现，此外还可以适配原生 servlet 或者其它的 web 框架。

在依次执行完拦截器和处理器后就得到了结果。

如果这时产生了异常，那么 DispatcherServlet 就会委托处理器异常解析器处理那些异常。

最后委托视图解析器渲染视图。

## 过滤器和拦截器有什么区别？
过滤器过滤的是 http 请求，它是 servlet 的规范之一，只能在有 web 容器时才能使用它。

拦截器拦截的是 http 请求对应的 java 方法，在过滤器之后执行。它是 spring 框架通过反射实现的一种机制，可以在任意的场景下使用。

## 转发与重定向的区别
重定向是客户端行为，当客户端发起请求，服务端将重定向的地址返回给客户端，然后客户端自动请求这个地址，服务端再把处理结果返回给它。相当于两次请求。

转发是服务端的行为，当客户端发起请求，这个请求可以在服务端内部多次传递，直到最后将结果返回客户端。转发只有一次请求。

## ____Spring事务传播机制有哪些？
[带你读懂Spring 事务——事务的传播机制](https://zhuanlan.zhihu.com/p/148504094)

PROPAGATION_

REQUIRED：如果当前存在事务，就加入；如果没有，就新建一个。

SUPPORTS：如果当前存在事务，就加入；如果没有，就以非事务的方式执行。

MANDATORY：如果当前存在事务，就加入；如果没有，就抛出异常。

REQUIRES_NEW：直接创建一个事务，如果已经有了，就挂起那个事务。

NOT_SUPPORTED：不使用事务，如果已经有了，就挂起事务。

NEVER：不使用事务，如果存在，就抛出异常。

NESTED：如果当前存在事务，就在事务内部执行事务；否则就跟 REQUIRED 一样。在这个机制中，子事务可以不影响父事务。

**挂起事务是什么意思？**就是 Spring 重新申请了一个连接。

*mysql 的隔离级别可以分为 global 和 session，每个 session 就是一个连接。*

**怎么实现子事务回滚不影响父事务？**spring 使用了 mysql 的 SAVEPOINT。*不过具体原理不清楚*。

## spring中有哪些设计模式？
1. 单例模式：？
2. 建造者模式：可以更加灵活地构建对象，在没有调用 build 方法之前都不会创建对象。
3. 工厂方法模式：ApplicationContext 和 BeanFactory 的 getBean 方法就是工厂方法。通过它可以得到一个 bean，但是 bean 具体是怎么创建的，我们不关心。它对外隐藏了实例化对象的过程，交给子类来实现。例如基于 xml 或者基于注解。`FactoryBean` 和 `@Bean` 也可以算工厂方法模式，可以创建第三方的 bean，但是隐藏创建过程。
4. 适配器模式：例如处理器适配器（HandlerAdapter），它可以适配多种不同的处理器。
5. 责任链模式：例如处理器拦截器（HandlerInterceptor），它和处理器组成了一条处理器执行链，每一个拦截器都会对请求做一些处理。可以增强委派责任的灵活性，可以动态地增加或者删除责任。
6. 模板方法模式：Spring 里面有很多地方都用到了模板方法模式，具体来说就是在某一个方法里面调用了一个抽象方法，然后这个抽象方法交给子类实现。

## BeanFactory和FactoryBean有什么区别？
BeanFactory 是维护 bean 的一个容器。

FactoryBean 是工厂方法模式，可以在继承它，然后在内部创建 bean。这样就对外部隐藏了创建 bean 的复杂过程。

## Spring会为被Configuration标注的类生成一个代理对象
Spring会为被Configuration标注的类生成一个代理对象，这样在调用被 `@Bean` 标注的方法时，就不会反复地生成对象，而是生成同一个对象。

`@Configuration` 可以被看作是一个工厂，被 `@Bean` 标注的方法可以被看作是一个工厂方法。

注意：被 `@Bean` 标注的方法不支持重载。

注意：`@Configuration` 中如果有 `BeanFactoryPostProcessor`，会导致 `@Configuration` 类被提前创建

## springboot
`@SpringBootApplication` 会加载主启动类目录下的所有类，不过会排除自动配置类。如果想要加载自动配置类，需要在 `META-INF/spring.factories` 下定义自动配置类名。

### springboot启动类注解
`@SpringBootApplication` 由三个注解组成：`@SpringBootConfiguration`，`@EnableAutoConfiguration` 和 `@ComponentScan`。

### SpringBootConfiguration
`@SpringBootConfiguration` 本质上只是一个 `@Configuration`，它还被标注了 `@Indexed`。**这个注解没啥好说的，下面稍微讲个题外话，就是 `@Indexed`。**

`@Indexed` 代表为模式注解（stereotype annotation）加上索引。可能对模式注解不太熟悉，其实就是 `@Repository`，`@Component`，`@Service`，`@Controller`，`@Configuration` 这些。其实 `org.springframework.stereotype` 包的名字就是 stereotype，里面的 `@Component` 被加了 `@Indexed` 注解，而其它的绝大部分模式注解又加上了 `@Component`。也有少部分是直接加的 `@Indexed`，例如 `@Persistent`。

那么它有啥用呢？[@Indexed 注解——博客园](https://www.cnblogs.com/54chensongxia/p/14389134.html)

虽然类路径扫描很快，但是还是有可能提升大型应用的启动性能。在编译的时候，创建一个静态列表即可。在这种模式下，组件扫描的所有目标模块（modules）都必须使用这个机制。

上面的话不难理解，主要解释一下什么是“静态列表”。其实它就长下面这样，位于 `META-INF/spring.components` 文件中。

```
io.github.yan624.xxx.bean.Dog=org.springframework.stereotype.Component
io.github.yan624.xxx.bean.service.AnimalService=org.springframework.stereotype.Component
io.github.yan624.xxx.config.AppConfig2=org.springframework.stereotype.Component
```

你可能会想，我经常用 springboot 咋没看见过这个文件？因为想要启用这个模式，你必须引入 `spring-context-indexer`。

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context-indexer</artifactId>
	<version>5.3.13</version>
	<optional>true</optional>
</dependency>
```

`@Bean` 是 java 注解，所以是没有被标注 `@Indexed`。不过 `@Bean` 一般位于被标注了 `@Indexed` 的 `@Configuration` 中，所以也能被感知到。

**不过在使用的时候需要注意**。如果 index 只出现在某些库中，而没有为整个项目构建 index，那么某些 bean 就无法被扫描到。你有条退路可以选，将 `spring.index.ignore` 设置为 `true`，可以在系统属性里设置，也可以在 `spring.properties` 中设置。

### ComponentScan
指明了扫描哪里的包，默认就是当前包。

[springboot @ComponentScan排除指定类](https://www.cnblogs.com/chwwww/p/14186422.html)

`excludeFilters` 属性可以指明排除哪些包，有几种模式可以选择：

1. ANNOTATION：排除指定注解
2. ASSIGNABLE_TYPE：排除指定类型
3. ASPECTJ：排除给定的 AspectJ 类型模式表达式
4. REGEX：排除给定的正则表达式
5. CUSTOM：自定义

`SpringBootApplication` 有一个自定义的 `AutoConfigurationExcludeFilter`，它的作用是排除一些自动配置类。可以看这个类的源码的注释。

### EnableAutoConfiguration
`@EnableAutoConfiguration` 由 `@AutoConfigurationPackage` 和 `@Import` 组成。

`@AutoConfigurationPackage` 指的是程序会记录被这个注解标注的类的包名。对于当前的场景，那就是会记录项目的根路径，也就是 `io.github.xxxx`。*你可以在你的项目中搜一下这个注解在哪里被用到了。应该就被 `SpringBootApplication` 用到了。*

`@Import` 主要是导入了 `AutoConfigurationImportSelector`，它可以扫描在 `META-INF/spring.factories` 中定义的自动配置类。

这个类保证了在主配置类中配置加载完之后，才去加载其它的配置类。

### springboot启动流程
[头秃了，二十三张图带你从源码了解SpringBoot启动流程！](https://zhuanlan.zhihu.com/p/301063931)

首先在主启动类中调用 SpringApplication 的 run 方法。

然后启动监听器，这个监听器主要用来监听启动过程，例如在环境创建好后触发，在上下文准备好后触发，run 方法结束后立即触发，失败后触发等等。（`SpringApplicationRunListeners` 和 `SpringApplicationRunListener`）

接着读取 application 配置文件，用来创建环境。

最后就是创建 IoC 容器，它（ApplicationContext）会扫描主启动类目录下的所有类（Component），还有自动配置的类，自动配置类在 `META-INF` 下的 `spring.factories` 文件里定义。

这个主要是通过主启动类上的注解进行配置的。

## starter是什么？
starter 里面有一些自动装配的类，pom 文件里还制定了各种 jar 包的依赖，用来简化开发的。

## JDKProxy和Cglib有什么区别？
jdk 代理是基于接口的代理，cglib 是基于类的代理。

cglib无法代理 final 类，也无法处理 final 方法。

jdk 代理通过反射机制实现，cglib 通过修改字节码生成子类来实现。

如果目标实现了接口，spring 默认使用 jdk。

如果目标没有实现接口，spring 默认使用 cglib。

SpringBoot默认是cglib动态代理，如果需要开启 jdk 代理，需要设置:

```yml
spring:
  aop:
    proxy-target-class: false

```

详情请看《惊人！Spring5 AOP 默认使用Cglib? 从现象到源码深度分析 》

## springboot默认启用aop不需要加@EnableAspectJAutoProxy
`AopAutoConfiguration`
















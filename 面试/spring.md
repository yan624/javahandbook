## 惊人！Spring5 AOP 默认使用Cglib? 从现象到源码深度分析 
[惊人！Spring5 AOP 默认使用Cglib? 从现象到源码深度分析](https://www.cnblogs.com/coderxiaohei/p/11758239.html)

## 什么是IoC？
IoC 的中文翻译是控制反转，它指的是将创建对象的控制权交给别人而不是自己。

Spring 就提供了 IoC 容器的功能，它可以通过配置自动创建对象，并且还可以维护对象与对象之间的依赖关系。

## 什么是依赖注入？
依赖注入就是说自己不需要维护对象与对象之间的依赖关系。

通过配置，IoC 容器会自动注入依赖的对象。

## 有哪些方法可以实现注入对象？
1. 通过 set 方法
2. 通过 构造器
3. 通过属性直接注入

## IoC有什么优点？
[为什么要用 spring 的 IOC 和 AOP? 用了 IOC 和 AOP 的优点缺点是什么](https://www.zhihu.com/question/19993030/answer/1712944965)

[为什么Spring中的IOC能够降低耦合性？](https://blog.csdn.net/tinpo_123/article/details/108718367)

IoC 可以实现资源复用。IoC 在为类创建一个实例对象后，不需要再重复创建。许多对象其实只要创建一次就够了。

IoC 还可以降低耦合度。对象的创建以及依赖关系完全取决于配置，并且可以让 IoC 容器自动注入。

## bean有哪些作用范围？
主要分为单例和多例。

单例指的是对于某个 bean 定义 一个 IoC 容器中只存在单份对象。

多例指的是可以存在多份对象，使用一次就会创建一个。

还有一些特殊的作用域，比如 request、session、application和websocket。它们只在对应的生命周期之中只存在一个对象。

request：每个 request 内都会创建一个 bean。在 request 的生命周期内只有单个。

session：每个 session 内都会创建一个 bean。在 session 的生命周期内只有单个。

application：在 ServletContext 的生命周期内只有单个。

websocket：在 WebSocket 的生命周期内只有单个。

## bean在什么时候销毁？
默认是项目关闭就销毁了

## 谈谈对aop的理解？
[什么是面向切面编程AOP？](https://www.zhihu.com/question/24863332/answer/350410712)

[为什么要用 spring 的 IOC 和 AOP? 用了 IOC 和 AOP 的优点缺点是什么](https://www.zhihu.com/question/19993030/answer/1712944965)

AOP 指的是面向切面编程。它是 OOP 的一种补充，也是一种编程范式。

它可以在不改变原有代码的情况下执行一些额外的代码。

Spring 中的 aop 实现主要集中在事务、日志记录和异常处理上。

AOP 有一些重要的概念，比如切入点、连接点、通知。

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

## bean初始化的完整流程（不用看）
[spring bean](https://www.bilibili.com/video/BV15b4y117RJ?p=143)

我后来自己也总结过，跟这个差不多。

1. 处理名称、检查缓存
2. 处理父子容器（查看父容器是否有 bean）
3. dependsOn（控制创建顺序）
4. 按照scope创建bean
5. **具体创建 bean**
	1. 创建（简单来说，优先选择标注 `@Autowired` 的构造器，如果没有标注并且有一个唯一的带参构造器，那么就使用这个；如果都不符合，就使用默认构造器（即使是私有的也可以创建）。此步只是用反射创建了一个空的对象）
	2. 依赖注入：通过匹配注解注入依赖（例如 `@Autowired`、`@Value`、`@Resource`）；通过名称匹配、类型匹配进行注入（比如 set 方法）；通过 xml 配置进行注入
	3. 初始化：处理 aware 接口，回调里面的方法；然后调用初始化方法，java 注解、spring 接口、自定义方法（需要xml配置，或者`@Bean(initMethod)`配置）；最后创建 aop 代理
6. 类型转换
7. **销毁**
	1. 单例 bean 的销毁时机在 ApplicationContext.close 的时候
	2. 多例 bean 的销毁时机由自己控制
	3. 其它的 bean 在作用域对象生命周期结束时销毁

## aop原理？

## Spring是怎么解决循环依赖的？（方法注入）
1. 一级缓存（singletonObjects）：限制 bean 在容器中只存在一份，也就是实现单例 bean。Spring 会到一级缓存里查看有没有 bean，如果没有再创建。一级缓存无法解决循环依赖。
2. 二级缓存（earlySingletonObjects）：想要解决代理对象产生的循环依赖问题，只需要将代理对象的创建提前即可，但是 Spring 只想在发生循环依赖时，才将创建提前。具体的做法是 Spring 创建 bean 的工厂对象，并把它放到三级缓存中，等其它对象要用到目标类时，再用目标类的工厂创建目标类的代理对象，并将代理对象放入二级缓存。这样，其它对象可以依赖注入这个代理对象，等到目标类要创建自己的代理对象时，也可以到二级缓存中直接拿出来。如果没有发生循环依赖，那么工厂对象直接创建目标类的对象，并放入二级缓存。工厂在被使用后，会从三级缓存中移除。
3. 三级缓存（singletonFactories）：就是在实例化 bean 的之后，进行依赖注入之前，将对象加入到三级缓存里。三级缓存无法解决 bean 有代理时产生的循环依赖问题，因为在从三级缓存中拿出的半成品对象，并不是代理的半成品对象，而是目标类的半成品对象。三级缓存解决了循环依赖。

## Spring是怎么解决循环依赖的？（构造器注入）
三级缓存无法解决构造器注入引发的循环依赖，因为对象连创建都无法创建。

解决的思想是延迟创建依赖的对象，先用一个代理对象（工厂对象）充当依赖对象。

使用 `@Lazy` 标注构造器中的参数即可，或者自己声明一个 `ObjectFactory<Target>` / `ObjectProvider<Target>`，让 spring 注入。

使用 javax 的 `Provider` 接口也可以，与上面的方法类似。

使用 `@Scope` 注解也可以，不过它只能配合组件扫描才能使用。`@Scope` 也会生成一个代理对象。

## Spring是怎么解决循环依赖的？（总结）
1. 如果是使用方法进行依赖注入，那么就使用三级缓存。
	1. 一级缓存：存放成品对象
	2. 二级缓存：存放工厂生产的对象（半成品对象或者代理对象）
	3. 三级缓存：存放工厂对象
2. 如果是使用构造器进行依赖注入（或者是多例循环依赖），那么就使用一个代理对象充当依赖的对象，这样可以延迟创建依赖的对象。
	1. `@Lazy`
	2. `ObjectFactory` / `ObjectProvider`
	3. `Provider`
	4. `@Scope`

spring 使用三级缓存解决的。

如果发生了循环依赖，spring 就会为目标类创建一个对象工厂，然后把它放到三级缓存里。

这个过程发生在创建 bean 之后，依赖注入之前。

如果在依赖注入的时候发生了循环依赖，那么三级缓存中的对象工厂就被拿来生成目标类的实例对象。

实例对象被存放在二级缓存里，同时还要移除三级缓存里的对象工厂。

这种做法相当于把创建一个对象拆解为实例化和初始化两步。

实例化好的对象可以先拿过来占个位子。

最后当初始化完后，成品对象被放在一级缓存里。平时读取对象也是在这个一级缓存里。

## 为什么要用三级缓存而不是二级？
[框架篇-43-循环依赖_铺垫_ProxyFactory_1](https://www.bilibili.com/video/BV15b4y117RJ?p=176)

因为 spring 的设计是代理对象在 bean 创建好之后再生成。

为了让代理对象提前生成，需要用三级缓存存储工厂对象。

其实只用二级缓存也可以，只需要把工厂对象和工厂对象生成的对象都存在二级缓存里就行了。

因为在得到工厂对象生成的对象之后，需要删除工厂对象。这样的话，两个对象是不会冲突的。

## 讲讲bean的创建过程（生命周期）
首先，spring 读取配置，创建 bean 的定义。然后根据 bean 的定义实例化对象。

然后开始依赖注入，这一步也会处理循环依赖的问题。

接下来就是调用 `Aware` 接口和 `BeanPostProcessor` 接口的方法。

在回调完之后，就开始初始化 bean。初始化方法分三种，分别是 java 的注解（`@PostConstruct`）、spring 的接口（`InitializingBean`）以及自定义的方法。它们是可以共存的，如果同时存在，就按刚才说的顺序调用。

初始化完之后就可以使用了。

当 bean 销毁时，也有三种方式。调用的顺序跟初始化方法的顺序一样。








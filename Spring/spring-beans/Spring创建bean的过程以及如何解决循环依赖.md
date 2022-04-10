> 本文基于 spring-beans:5.3.13

在创建 bean 时，可能需要处理循环依赖这一问题。例如 ServiceA 需要用到 ServiceB，而 ServiceB 也需要用到 ServiceA。

本文以 `ApplicationContext` 中 bean 的创建为例，梳理一下 bean 的创建流程以及 spring 如何解决循环依赖。

本文从 `AbstractApplicationContext#refresh()` 方法开始，它意味着 spring 开始准备程序的上下文，包括 IoC 容器。

*这里有多处都会调用 `BeanFactory` 的 `getBean()` 方法。*

下面对本章的结构做简要说明：

1. `getBean`：`AbstractBeanFactory` 的方法。
	1. `getSingleton`：`DefaultSingletonBeanRegistry` 的方法。此方法使用传入的 `ObjectFactory` 参数获得 bean，`ObjectFactory#getObject()` 使用 `createBean()` 创建对象。
	2. `createBean`：`AbstractAutowireCapableBeanFactory` 的方法。
	3. `doCreateBean`：`AbstractAutowireCapableBeanFactory` 的方法。此方法开始真正地创建对象。
	4. `populateBean`：进行依赖注入。
	5. bean创建流程的简要总结：如果觉得逻辑过于混乱，可以结合此节理清头绪。
2. 如何处理构造器依赖注入的循环依赖问题

## getBean
`AbstractBeanFactory#getBean()` 方法简单地调用了 `doGetBean()` 方法，因此可以将目光集中到后者身上。

`doGetBean()` 的代码虽多，但是逻辑比较简单。代码可以简化如下形式，重点在第 3 步。

```java
// 1. 返回 bean 的规范名称
String beanName = transformedBeanName(name);
Object beanInstance;

// 2. 检查单例缓存
Object sharedInstance = getSingleton(beanName);
if (sharedInstance != null && args == null){
	// ...
// 3. 如果没有缓存，就在此处创建 bean 的实例
} else {
	// ...
}

// 4. 调整 bean 实例
return adaptBeanInstance(name, beanInstance, requiredType);
```

具体来说，内部大致可分为如下几步（主要关注 3.6.1 步即可，见《doCreateBean》小节）：

1. 返回 bean 的规范名称：去除工厂对象的前缀或者解析 bean 的别名。
2. 检查单例缓存。如果存在，就代表之前已经手动注册过。<font style="color:red">②注意，如果出现了循环依赖，那么此步骤还会通过三级缓存中的 `ObjectFactory` 生成具体的 bean，并将其存入二级缓存。</font>
	1. `getObjectForBeanInstance`：得到 bean 的实例对象，要么直接返回，要么通过 `FactoryBean` 获得。
3. **如果没有缓存，就在此处创建 bean 的实例**。
	1. 如果正在创建多例 bean，就直接抛出异常。三级缓存无法解决多例情况下的循环依赖。
	2. 如果存在父容器并且当前容器不包含该 bean 的定义（`BeanDefinition`），就检查 bean 是否存在于父容器。如果存在，就直接返回。
	3. 开启 `StartupStep`，用于追踪一些步骤的执行时间或其它的性能。
	4. 获得 bean 的定义（`BeanDefinition`）。
	5. 确保取决于（depends on）当前 bean 的那些 beans 已被初始化，*注意和依赖注入区分*。
	6. **创建 bean 实例**。每步的最后都使用了 `getObjectForBeanInstance`。
		1. 如果是单例模式，就 `DefaultSingletonBeanRegistry#getSingleton`。注意，这里使用 `ObjectFactory` 创建 bean。
		2. 如果是多例模式，就直接创建 bean，并且标记当前正在创建多例 bean。*此步可能可以与 3.1 联动，但是我对多例模式不熟。*
		3. 如果是其它作用域，就 `Scope#get`。注意，这里使用 `ObjectFactory` 创建 bean。
4. 调整 bean 实例：如果没有提供 `requiredType` 参数，就直接将实例对象强转为方法的泛型；否则使用 `TypeConverter` 进行类型转换。

步骤 2 的手动注册指的是调用了 `DefaultSingletonBeanRegistry#registerSingleton()`，该方法是对外暴露的 public 方法，它直接将 bean 存入一级缓存。`DefaultListableBeanFactory#registerSingleton()` 简单地调用了上述方法，也就是说你可以使用 `BeanFactory` 手动注册一个 bean，spring 在此步会感知到你手动注册的 bean。注：上述两个类本身就具有继承关系。

第 3.6 步是最关键的一环。下面的小节中将介绍单例模式下的循环依赖，即 3.6.1 步。

### getSingleton
`DefaultSingletonBeanRegistry#getSingleton` 首先用 `synchronized` 锁住一级缓存，然后从中取出单例对象。如果已存在就直接返回，否则就进行下一步操作。

接下来的代码逻辑较为简单。首先标记该 bean 正在被创建，然后开始调用 `ObjectFactory#getObject()`，也就是开始创建对象，最后将正在被创建的标记取消。

<font style="color:red">③在创建完成后就将单例对象放入一级缓存中，并返回该对象。</font>

`ObjectFactory#getObject()` 是创建对象的关键步骤，也是解决循环依赖的关键步骤。

`ObjectFactory` 实际上是 3.6.1 步创建的匿名内部类，`getObject()` 简单地调用了 `createBean()` 方法。

### createBean
`createBean()` 主要调用了 `doCreateBean()` 方法。

### doCreateBean
!> 此节是创建 bean 的关键步骤，也是解决循环依赖的关键步骤。实际上从 `getBean()` 的调用开始，到此节为止，中间的内容并不是很重要。

第一步，通过 `BeanDefinition` 实例化 bean。不过会先用 `BeanWrapper` 封装这个 bean，它提供了分析和操作 javabean 的功能，例如获取和设置属性值（单独或批量）、获取属性描述符以及查询属性的可读写性。

第二步，<font style="color:red">①如果检测到产生了循环依赖，就将对象工厂 `ObjectFactory` 放入三级缓存</font>。

第三步，开始初始化 bean。

首先使用 `populateBean()` 方法进行依赖注入。

然后马上使用 `initializeBean()` 方法进行初始化。方法内部实际上还调用了所有 `Aware` 接口中的方法以及 `BeanPostProcessor` 接口中的两个方法，分别在初始化之前和之后调用。而方法内部真正的初始化操作可由自己配置，一共有三种：java 注解、spring 接口和自定义的初始化方法。

第四步，如果目标类有代理，就将 bean 替换为代理对象的 bean。

第五步，如果有需要的话（实现了销毁方法），将初始化完毕的 bean 注册进待销毁的容器中。

第六步，返回初始化完的 bean。

### populateBean
假设存在依赖关系 A->B->A。从《getBean》开始到《doCreateBean》结束，一直都是创建 A 的流程。如果在依赖注入时发现依赖的 B 对象也依赖 A，该怎么办？这就是所谓的循环依赖，也是 `populateBean()` 解决的问题。

`populateBean()` 委托 `AutowiredAnnotationBeanPostProcessor` 处理需要自动注入的属性。支持以下注解：1）`@Autowired`；2）`@Value`；3）`@Inject`

`AutowiredAnnotationBeanPostProcessor#postProcessProperties()` 在进行一连串操作后，发现 A 依赖 B，那么就调用 `beanFactory.getBean("B")`，以此获得 B 的实例对象。

由于目前容器中不存在 B，因此又要走一遍上面的所有流程，最后又到了 `populateBean()`。此时，发现 B 又依赖 A，于是调用 `beanFactory.getBean("A")`。

<font style="color:red">②注意此时《getBean》中的步骤 2 可以从三级缓存中通过 `ObjectFactory` 得到 bean。</font>

因此 B 的循环依赖被打破，从而 B 创建成功。所有方法一层一层退出，让 A 依赖注入 B，从而 A 也创建成功。

### bean创建流程的简要总结
1. spring 想要获取一个 bean，如果不存在则创建，存在则直接返回（《getBean》、《getSingleton》）
2. 如果 spring 检测到产生了循环依赖，就为目标类创建一个对象工厂 `ObjectFactory`，并放入**三级缓存**（《doCreateBean》）
3. 实例化 bean（《doCreateBean》）
4. 依赖注入（《doCreateBean》、《populateBean》）
	1. 如果发生了循环依赖 A->B->A，在为 B 注入 A 时，使用三级缓存中的对象工厂生成对象（普通对象或代理对象），并将其放入**二级缓存**，然后移除三级缓存中的对象工厂。循环依赖被打破
5. 调用所有 `Aware` 接口中的方法（《doCreateBean》）
6. 在初始化方法前后调用所有 `BeanPostProcessor` 接口中的方法（《doCreateBean》）
7. 调用初始化方法（《doCreateBean》）
8. 如果目标类有代理，就将 bean 替换为二级缓存中的代理对象（《doCreateBean》）
9. 如果有需要，就将 bean 注册进待销毁的容器（《doCreateBean》）
10. 将成品对象存入**一级缓存**，然后移除二级缓存中由对象工厂生成的对象（《getSingleton》）
11. 如果 bean 是 `FactoryBean` 类型，就进一步获取对象（《getBean》）

## 如何处理单例bean构造器依赖注入的循环依赖问题
三级缓存无法解决在使用构造器依赖注入时产生的循环依赖问题。

解决办法是懒惰加载，也就是为目标类创建一个代理对象。在出现循环依赖时，先注入代理对象。等真正用到目标类时，在让代理对象生成目标类的对象。

以下四种方式均可：

1. `@Lazy`
2. `ObjectFactory` / `ObjectProvider`
3. `Provider`
4. `@Scope`

## 如何处理多例bean依赖注入的循环依赖问题
同上。

## 如何处理其它作用域的循环依赖问题
同上。

## 既然用代理对象就可以解决循环依赖为什么不取消三级缓存
可能是因为有些类不能懒惰加载，必须要尽快创建然后投入使用。

## spring具体在哪个步骤处理循环依赖问题
实例化对象之后，依赖注入之前（或者初始化对象之前，初始化对象在依赖注入的后面一步）。




















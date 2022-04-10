[spring源码阅读——GenericBeanDefinition及其子类](https://blog.csdn.net/dhaiuda/article/details/83311495)

[【小家Spring】Spring IoC容器中核心定义之------BeanDefinition深入分析（RootBeanDefinition、ChildBeanDefinition...）](https://blog.51cto.com/u_3631118/3121425)

spring 通过不同的 `BeanDefinition` 保存不同的 bean 的信息，例如 `@Bean`、`@Configuration`、`@Component` 这些不同的 bean。最后汇总成 `RootBeanDefinition`。

`BeanDefinition` 的父子关系类似于 java 的继承，子类可以覆盖父类的属性或者方法。
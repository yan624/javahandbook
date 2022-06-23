Spring 4 引入了字符串格式化系统。它是类型转换系统的一个子集，内部的功能也来自于类型转换系统暴露的接口。

Spring 提供了以下四种格式化接口：

1. `Parser`：解析器，解析字符串并生成对象 `T`。可转换类型是 `String.class` -> `T.class`。
2. `Printer`：打印器，打印 `T` 类型的对象。可转换类型是 `T.class` -> `String.class`。
3. `Formatter`：实现了 `Parser` 和 `Printer`。都可以转换。
4. `AnnotationFormatterFactory`：根据属性上的特定注解创建 formatter。取决于创建的 formatter。

字符串格式化系统也使用了 `ConversionService` 管理所有转换器。

## FormattingConversionService
`FormattingConversionService` 的继承体系如下所示，可以观察到其继承自 `GenericConversionService`，其中已经实现了绝大部分的功能，例如注册转换器（converter）。`FormatterRegistry` 是注册表，用于在服务中注册四种格式化接口，类似于 `ConverterRegistry`。值得注意的是，spring.formatter 基于类型转换系统，因此在注册时，实际上注册的都是这些格式化接口的 `GenericConverter` 或者 `ConditionalGenericConverter` 适配器。条件转换器主要用于匹配属性上的注解，例如 `@DateTimeFormat`。

![FormattingConversionService](img/DefaultFormattingConversionService.png ':size=70%')

最下面的默认实现预先加载了大量的已经实现的 `Formatter`。

## 支持的Formatter
Spring 目前支持两种类型的格式化：

1. 日期
	1. datetime (java 1.0)
	2. joda (已废弃)
	3. standard (java 8, JSR-310)
2. 数字
	1. number
	2. money (JSR-354)






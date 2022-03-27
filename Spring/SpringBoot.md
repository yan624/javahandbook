SpringBoot 针对 Spring 和第三方库采取他们测试过的配置观点，这样开发人员就可以不费吹灰之力地快速开始应用的编写。

SpringBoot 直接内置了 Tomcat，Jetty 以及 Undertow（不在需要部署 WAR 文件）；提供测试好的“starter”依赖以简化你构建配置；尽可能地自动配置 Spring 和第三方库；提供生产必备的功能；不再需要代码生产，不再需要 XML 配置。

简单来说，以前开发人员在开发网站时需要控制各种框架的版本，防止版本冲突，这非常麻烦。现在，Spring 直接提供一套他们测试过的版本搭配以及其它零碎配置，开发人员开箱即用，几乎不需要再手写配置文件。

没必要把 SpringBoot 想的很复杂，它只是把其它框架整合起来而已。

可以从 SpringBoot 的[官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/)中学习，但是里面太多，可以把它当做手册来用，不会的时候再去看。下面记录一些常用的内容。

## 创建SpringBoot项目
可以到 [SpringBoot 官网](https://start.spring.io/)创建并下载，然后导入到 idea，也可以直接从 idea 创建。

该项目默认已经创建启动类，只要运行该类就在 8080 端口运行了 tomcat 服务器。接下来只需要实现业务代码即可。可以看到已经不再需要书写各种配置文件，SpringBoot 已经帮我们配好了。

不过还是有必要了解一下如何修改配置。

## SpringBoot配置
1. 修改banner
2. 



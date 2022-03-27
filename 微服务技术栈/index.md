由于微服务相关的框架以及技术错综复杂，本章节首页整理一下微服务领域的各种技术。

可以把 Spring Cloud Alibaba 看作是 Dubbo 体系的重生版。Dubbo 体系是多年以前的搭配，目前活跃度不高。


| 特性 | Dubbo 体系 | Spring Cloud | Spring Cloud Alibaba |
|------|------------|--------------|----------------------|
分布式配置中心 |	dubbo | Config | nacos
服务注册与发现 | zookeeper | Eureka | nacos
路由 |
服务之间的通信 | | Feign | dubbo
负载均衡 | | Ribbon | dubbo
熔断机制 | | Hystrix |
Global locks | 
Leadership election and cluster state | 
Distributed messaging | 
分布式消息 | | Bus | nacos

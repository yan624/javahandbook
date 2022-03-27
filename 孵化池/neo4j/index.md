我发现国内互联网上似乎只有很少关于 Spring Data Neo4j 的文章，即使有较为热门的文章，也是一些基础教学，这些教学内容在官方文档（无论是 neo4j 的文档还是 Spring Data Neo4j 的文档）中早有提及。国内几乎没有文章介绍怎样构建一个基于 neo4j 且较为复杂的系统（如果有可以附上链接）。为此，本人决定以教程的形式描述本系列内容。

本系列主要参考了 Neo4j、Spring Data Neo4j 和 Cypher-DSL 的官方文档，链接在《参考文档》中。其它的参考内容将在对应的文章中给出。

本系列主要使用 spring-data-neo4j (6+) 作为持久层框架。可能国内程序员对 Spring Data 这个项目不是很了解，因为我们用的比较多的框架是 mybatis，很有必要在此处简要介绍一下这个项目。这对之后理解某个概念非常有帮助。

Spring Data 主要的功能是完成对象映射、对象创建、字段和属性的访问。想要知道原理的可以参考[官方文档](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#mapping.fundamentals)。Spring Data repository abstraction 的目标是大量减少模板代码的数量，这些代码指的是**用于多种持久存储的数据访问层实现**，抽象的中心接口是 `Repository`。注意，上面陈述的语句很重要。在 `Repository` 中提供了大量的模板代码，它可以帮助程序员快速地完成 CRUD、分页、规范查询、样例查询。你可以理解为 Mybatis-Plus 的 Spring 实现版，不过这个实现横跨了多种数据存储，对很多数据库都是通用的。

你可能会想上面说了那么多，跟 neo4j 有什么关系？重点就在 `Repository` 上。个人认为基于 Repository 实现 Spring Data Neo4j 限制了图数据库的灵活性，严格来说是目前有所限制。据我花费五天左右时间搜索资料后发现，Spring Data Neo4j 无法**动态地**处理 Neo4j 中的关系（relationship）。

?> 为了节省文章空间，所有代码均有所简化。

## 参考文档 <!-- {docsify-ignore-all} -->
1. [Neo4j documentation](https://neo4j.com/docs/)
2. [Neo4j 开发者文档](https://neo4j.com/developer/get-started/)
3. [Graph database concepts](https://neo4j.com/docs/getting-started/current/graphdb-concepts/)
4. [spring-data-neo4j 文档](https://docs.spring.io/spring-data/neo4j/docs/current/reference/html/#getting-started)
5. [The Neo4j Cypher-DSL](https://neo4j-contrib.github.io/cypher-dsl/current/#introduction)


## 理解生态体系
问：[neo4j-ogm](https://github.com/neo4j/neo4j-ogm) 和 [spring-data-neo4j](https://github.com/spring-projects/spring-data-neo4j) 有什么关系？我在网上看见了这两个框架，它们的作用看起来类似？  
答：据 [spring-data-neo4j 文档](https://docs.spring.io/spring-data/neo4j/docs/current/reference/html/#sdn-related-to-ogm) 介绍，[neo4j-ogm](https://github.com/neo4j/neo4j-ogm) 是一个对象图谱映射（Object Graph Mapping，OGM）库，主要被 Spring Data Neo4j 以前的版本当作后端，承担了将节点和关系映射到领域对象中的重担。当前版本的 Spring Data Neo4j (6+) 不再需要，也不支持 Neo4j-OGM。

问：什么是 [spring-data-neo4j](https://github.com/spring-projects/spring-data-neo4j) ？  
答：据 [spring-data-neo4j 文档](https://docs.spring.io/spring-data/neo4j/docs/current/reference/html/#what-is-sdn) 介绍，当前版本的 Spring Data Neo4j（下简称为 SDN） 是 Spring Data Neo4j + Neo4j-OGM 的继承者。Neo4j-OGM 的*独立层（separate layer）*已被 Spring 的基础设施取代，但其概念仍然适用。SDN 是下一代 Spring Data 的模块之一，由 [Neo4j, Inc.](https://neo4j.com/) 创建、维护并且与 [VMware](https://www.vmware.com/) 的 Spring 数据团队紧密合作。



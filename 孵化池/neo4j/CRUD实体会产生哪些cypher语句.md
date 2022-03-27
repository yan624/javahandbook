## 新增三元组
!> emmm，老倒霉蛋了。在写完这节之后，我发现 [SDN 的官方文档](https://docs.spring.io/spring-data/neo4j/docs/current/reference/html/#query-creation.save) 已经解释过了。与下面的解释类似。总而言之，需要注意的是，SDN 在执行 `save` 时，它会移除实体的所有关系，然后再创建想要添加的关系。这是为了保证图模型与 java 世界同步。

### 使用@Relationship
在《漫威图谱——欢迎来到静态的世界·新增三元组》中，我们为“绿箭侠漫画”关联了“绿箭侠”和“司机侠”，这创建了两条关系。事实上，SDN 为我们执行了**七条 cypher 语句**。逻辑很简单，下面我分别做解释。

```cypher
// 为两个已知结点建立两条关系
OPTIONAL MATCH (hlp:`ComicIssue`) WHERE id(hlp) = $__id__ WITH hlp WHERE hlp IS NULL CREATE (comicIssue:`ComicIssue`) SET comicIssue = $__properties__ RETURN comicIssue UNION MATCH (comicIssue:`ComicIssue`) WHERE id(comicIssue) = $__id__ SET comicIssue += $__properties__ RETURN comicIssue
MATCH (startNode)-[rel:`INCLUDES`]->(:`Character`) WHERE (id(startNode) = $fromId AND NOT (id(rel) IN $__knownRelationShipIds__)) DELETE rel
MATCH (startNode)-[rel:`INCLUDES`]->(:`Character`) WHERE (id(startNode) = $fromId AND NOT (id(rel) IN $__knownRelationShipIds__)) DELETE rel

OPTIONAL MATCH (hlp:`Character`) WHERE id(hlp) = $__id__ WITH hlp WHERE hlp IS NULL CREATE (character:`Character`) SET character = $__properties__ RETURN character UNION MATCH (character:`Character`) WHERE id(character) = $__id__ SET character += $__properties__ RETURN character
MATCH (startNode) WHERE id(startNode) = $fromId MATCH (endNode) WHERE id(endNode) = $toId MERGE (startNode)-[relProps:`INCLUDES`]->(endNode) RETURN id(relProps)
OPTIONAL MATCH (hlp:`Character`) WHERE id(hlp) = $__id__ WITH hlp WHERE hlp IS NULL CREATE (character:`Character`) SET character = $__properties__ RETURN character UNION MATCH (character:`Character`) WHERE id(character) = $__id__ SET character += $__properties__ RETURN character
MATCH (startNode) WHERE id(startNode) = $fromId MATCH (endNode) WHERE id(endNode) = $toId MERGE (startNode)-[relProps:`INCLUDES`]->(endNode) RETURN id(relProps)
```

1. **选择性匹配漫画（`ComicIssue`）。如果漫画是空的，那么就创建它并且设置属性。然后再做一次匹配，更新漫画中的属性（`+=` 代表更新）。**注意这里做了两次匹配，主要是因为如果漫画是空的，第二条语句就无法执行了。为此，第一条匹配做了一次检查，如果漫画不存在就创建它，这样第二条语句其实就是冗余的。
2. **匹配以 `startNode` 为起始点的所有路径，如果有条路径是未知的，就删除它。**这里的“未知”指的是相对于传入的数据而言。假设 neo4j 中有 `A->B` 和 `A->C` 两条路径。现在我们传入 `A->D`，那么 SDN 帮我们删除 `A->B` 和 `A->C`。你可能已经发现这有点坑了。
3. **再做一次 2。**不知道为什么。可能是因为 `$__knownRelationShipIds__` 中的值不一样。
4. **选择性匹配角色（`Character`）。xxxxxxx。**与 1 的逻辑一致。
5. **匹配起点（`$fromId`）和终点（`$toId`），然后 `merge` 关系。**`merge` 代表关系原本存在则不变，不存在则创建。*奇怪就奇怪在 SDN 为啥要执行步骤 2，3。为啥把关系删了！！！*
6. **与 4 的逻辑一致。**
7. **与 5 的逻辑一致。创建另一个角色的关系。**

补充说明：`OPTIONAL MATCH` 与 `MATCH` 类似，区别在于如果语句没有匹配到任何内容，那么模式（pattern）就是 `null`。主要用于 `hlp IS NULL`。你在 neo4j desktop 中分别试一下以下两条语句：

```cypher
// 返回“nothing matched”
OPTIONAL MATCH (c:ComicIssue {name: "蜘蛛侠"}) WHERE c is NULL RETURN "nothing matched"
// 啥也没返回
MATCH (c:ComicIssue {name: "蜘蛛侠"}) WHERE c is NULL RETURN "nothing matched"
```

### 使用@Relationship和@RelationshipProperties
在《漫威图谱——欢迎来到静态的世界·CRUD关系》中，我们为“绿箭侠漫画”关联了“闪电侠”，这创建了一条关系。SDN 为我们创建了**五条 cypher 语句**。

```cypher
// 为一个已知结点建立一条关系，借助 `@RelationshipProperties`
OPTIONAL MATCH (hlp:`ComicIssue`) WHERE id(hlp) = $__id__ WITH hlp WHERE hlp IS NULL CREATE (comicIssue:`ComicIssue`) SET comicIssue = $__properties__ RETURN comicIssue UNION MATCH (comicIssue:`ComicIssue`) WHERE id(comicIssue) = $__id__ SET comicIssue += $__properties__ RETURN comicIssue
MATCH (startNode)-[rel:`INCLUDES`]->(:`Character`) WHERE (id(startNode) = $fromId AND NOT (id(rel) IN $__knownRelationShipIds__)) DELETE rel

OPTIONAL MATCH (hlp:`Character`) WHERE id(hlp) = $__id__ WITH hlp WHERE hlp IS NULL CREATE (character:`Character`) SET character = $__properties__ RETURN character UNION MATCH (character:`Character`) WHERE id(character) = $__id__ SET character += $__properties__ RETURN character
MATCH (startNode:`ComicIssue`) WHERE id(startNode) = $fromId MATCH (endNode) WHERE id(endNode) = $toId CREATE (startNode)-[relProps:`INCLUDES`]->(endNode) SET relProps += $__properties__ RETURN id(relProps)
MATCH (startNode)-[rel:`INCLUDES`]->(:`Character`) WHERE (id(startNode) = $fromId AND NOT (id(rel) IN $__knownRelationShipIds__)) DELETE rel
```
1. **选择性匹配漫画**，与上节类似。
2. **删除未知关系**，与上节类似。
3. **选择性匹配角色**，与上节类似。
4. **匹配起点和终点，起点必须是漫画（`ComicIssue`），然后 `create` 关系并更新（*为什么不是设置？*）关系的属性。**由于我们使用了 `@RelationshipProperties`，因此这步很有必要，SDN 需要为我们设置关系的属性。
5. **删除未知关系。**
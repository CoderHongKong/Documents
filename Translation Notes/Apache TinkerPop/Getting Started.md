# 入门

- Apache TinkerPop 是一个开源的图形计算框架。在其内部，TinkerPop代表大量的能力和技术，还有一个额外拓展的第三方贡献的图形库和系统世界。TinkerPop的生态系统对于所有经验的新手来说显得很复杂，特别是在第一次浏览参考文档时。

- 所以，你从哪里开始使用TinkerPop呢？如何快速投入并获得成效？好吧 - Gremlin，TinkerPop最知名的公民，在这里帮助这个30分钟的教程。没错-在短短30分钟内，你也可以使用TinkerPop开始构建图形应用程序。欢迎来到TinkerPop锻炼 - 你的Gremlin！

![image](http://tinkerpop.apache.org/docs/3.3.4/images/gremlin-gym.png)

## 前5分钟

- 使用TinkerPop只需五分钟即可学到很多东西，但在这之前，适当地介绍一下你的教练是合适的。认识Gremlin！

![image](http://tinkerpop.apache.org/docs/3.3.4/images/gremlin-standing.png)

- Gremlin帮助你导航图的顶点和边。他实质上是图数据库的查询语言，类似于SQL是关系型数据库的查询语言。为了告诉Gremlin应该怎样“遍历”图表（即：你希望你的查询做什么，）你需要一种方法以他能理解的语言为他提供命令——当然，这种语言叫做“Gremlin”。为了这个任务，你需要TinkerPop的最重要的工具之一：Gremlin控制台。

- 注意：你不确定什么是顶点和边？这个话题将在下一个章节介绍，但是请允许本教程带领你先使用Gremlin控制台，以便你对于这个工具有个初始认识，该工具将帮准学习经验。

- 下载控制台，解压并且启动它：

```
$ unzip apache-tinkerpop-gremlin-console-3.3.4-bin.zip
$ cd apache-tinkerpop-gremlin-console-3.3.4
$ bin/gremlin.sh

         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----
plugin activated: tinkerpop.server
plugin activated: tinkerpop.utilities
plugin activated: tinkerpop.tinkergraph
gremlin>
```

- Gremlin控制台是一个REPL环境，它提供了一个学习Gremlin的好方法，因为你能立即获得输入代码的反馈。这消除了“创建一个项目”来尝试更复杂的需求。然而，控制台不只是“入门”。你将发现你自己将它用于各种与TinkerPop相关的活动，例如：加载数据，管理图表，计算复杂遍历，等等。

- 要让Gremlin遍历图表，你需要一个图表实例，他拥有图表的结构和数据。TinkerPop是跨越了不同的图表数据库和不同的图表处理器的抽象层，因此你可以从控制台中选择许多表图实例进行实例化。然而，最好的图表实例是TinkerGraph。TinkerGraph是快速的内存中的图表数据库，具有少量配置选项，这使它成为初学者的一个好的选择。

- **TIP** TinkerGraph对于初学者来说不只是个玩具。它可以分析从大图中获取子图，处理变化不是太大的小静态图，编写单元测试以及图可以适用内存的其他用例。

- **TIP** 出于“入门”的目的，要抵制住挖掘更复杂数据库的诱惑，它有很多配置选项，或者研究如何让Gremlin Server正常工作。本指南介绍的基础知识，为TinkerPop提供的所有其他功能奠定了良好的基础。

- 为了使你的学习过程更轻松，从TinkerPop的“玩具”图表之一开始。这些是“小”图，设计用于快速开始查询。熟悉它们是很好的，因为几乎所有所有TinkerPop文档是基于它们，并且当你需要帮助以及不得不进入邮件列表时，将失败的案例放置在玩具图形的上下文中通常可以快速获得问题的答案。

- **TIP** 当邮件列表或者StackOverflow上询问关于Gremlim问题时，包含示例图通常总是有帮助的，以便那些尝试解答你问题的人准确明白你拥有哪种图形，并且可以将精力集中在一个好的答案上，而不是自己尝试构建示例数据。这个示例图应该只是一个简单的Gremlin脚本，它可以剪切并粘贴到Gremlin控制台回话中。

- 对于你的第一个图，使用“现代”图，如下所示：

![image](http://tinkerpop.apache.org/docs/3.3.4/images/tinkerpop-modern.png)

它可以通过这种方式在控制台上被实例化：
```
gremlin> graph = TinkerFactory.createModern()
==>tinkergraph[vertices:6 edges:6]
gremlin> g = graph.traversal()
==>graphtraversalsource[tinkergraph[vertices:6 edges:6], standard]
```

- 第一命令创建了名叫graph一个图实例，他从而提供了你想要Gremlin去遍历的数据引用。不幸的是，只提供graph不能提供给Gremlin足够的上下文去做它的工作。你还要做一些叫TraversalSource的事情，它通过第二个命令生成。TraversalSource给Gremlin提供了额外的信息（例如：遍历策略的应用和遍历引擎的使用），它提供了指导如何去执行遍历图。
- 有了 TraversalSource g 可以让Gremlin遍历图表：

```
gremlin> g.V() //1\
==>v[1]
==>v[2]
==>v[3]
==>v[4]
==>v[5]
==>v[6]
gremlin> g.V(1) //2\
==>v[1]
gremlin> g.V(1).values('name') //3\
==>marko
gremlin> g.V(1).outE('knows') //4\
==>e[7][1-knows->2]
==>e[8][1-knows->4]
gremlin> g.V(1).outE('knows').inV().values('name') //5\
==>vadas
==>josh
gremlin> g.V(1).out('knows').values('name') //6\
==>vadas
==>josh
gremlin> g.V(1).out('knows').has('age', gt(30)).values('name') //7\
==>josh
```

1. 获得图的所有顶点。
2. 获得拥有唯一标识“1”的顶点。
3. 获得顶点上name属性的值，其拥有唯一标识“1”。
4. 获得具有拥有唯一标识“1”的顶点标签为“knows”的边。
5. 获取拥有唯一标识“1”“knows”顶点的人名。
6. 注意，当使用 outE().inV()时，如上一命令所示，可以缩短调整为out()（类似于inE().inV()和in用于入边）。
7. 获取人物顶点为“1”且已知他的年龄大于30岁的人名。

- **重点** Traversal是指上是一个迭代器，所以如果你有编码类似于  x = g.V()，x 不包含g.V()的查询结果。相反，该语句将Iteror赋值给x。为了获取结果，你然后需要通过x迭代。这个理解是重要的，是因为在控制台上下文输入g.V()会立即返回值。控制台为你做了一些魔法，通过注意到g.v()返回一个迭代器然后自动迭代出结果。简而言之，当在控制台外部编写Gremlin时，请始终是记住，你必须以某种方式手动迭代你的遍历，以便它可以执行任何操作。“迭代你的遍历”的概念在Gremlin Console Tutorial会进一步描述。

- 在Gremlin的前五分钟，你已经安装了Gremlin的控制台，实例化Graph和TraversalSource，编写一下遍历并期望大致学到关于TinkerPop的一些内容。你只触及了所知道的表面，但这些成就将帮助你理解接下来更详细的部分。
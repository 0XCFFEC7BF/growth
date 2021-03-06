每个人都是架构师——如何设计一个博客系统
---

每一个程序员都是架构师。平时在我们工作的时候，架构师这个Title都被那些非常有经历的开发人员占据着。然而，如果你喜欢刷刷Github，喜欢做一些有意思的东西，那么你也将是一个架构师。

###如何构建一个博客系统

####如果你需要帮人搭建一个博客你先会想到什么？

先问一个问题，如果要让你搭建一个博客你会想到什么技术解决方案？

1. 静态博客（类似于GitHub Page）
2. 动态博客（可以在线更新，如WordPress）
3. 半动态的静态博客（可以动态更新，但是依赖于后台构建系统）
4. 使用第三方博客

这只是基本的骨架。因此如果只有这点需求，我们无法规划出整体的方案。现在我们又多了一点需求，我们要求是独立的博客，这样我们就把第4个方案去掉了。但是就现在的过程来说，我们还是有三个方案。

接着，我们就需要看看Ta需要怎样的博客，以及他有怎样的更新频率？以及他所能接受的价格？

先说说介格——从价格上来说，静态博客是最便宜的，可以使用AWS S3或者国内的云存储等等。从费用上来说，一个月只需要几块钱，并且快速稳定，可以接受大量的流量访问。而动态博客就贵了很多倍——我们需要一直开着这个服务器，并且如果用户的数量比较大，我们就需要考虑使用缓存。用户数量再增加，我们就需要更多地服务器了。而对于半动态的静态博客来说，需要有一个Hook检测文章的修改，这样的Hook可以是一个客户端。当修改发生的时候，运行服务器，随后生成静态网页。最后，这个网页接部署到静态服务器上。

从操作难度上来说，动态博客是最简单的，静态博客紧随其后，半动态的静态博客是最难的。

整的性价比考虑如下：

 x  |动态博客  | 静态博客 | 半动态的静态博客
------|--------|---------|-------
价格 | 几十到几百元 | 几元 | 依赖于更新频率 几元~几十元
难度 | 容易   | 稍有难度 | 难度稍大
运维 | 不容易 |容易  | 容易
数据存储 | 数据库 | 无 | 基于git的数据库

现在，我们已经达到了一定的共识。现在，我们已经有了几个方案可以提用户选择。而这时，我们并不了解进一下的需求，只能等下面的结果。

客户需要可以看到文章的修改变化，这时就去除了静态博客。现在还有第1和第3种方案可以选，考虑到第3种方案实现难度比较大，不易短期内实现。并且第3种方案可以依赖于第1种方案，就采取了动态博客的方案。

但是，问题实现上才刚刚开始。

####我们使用怎样的技术？

作为一个团队，我们需要优先考虑这个问题。使用怎样的技术解决方案？而这是一个更复杂的问题，这取决于我们团队的技术组成，以及未来的团队组成。

如果在现有的系统中，我们使用的是Java语言。并不意味着，每个人都喜欢使用Java语言。因为随着团队的变动，做这个技术决定的那些人有可能已经不在这个团队里。并且即使那些人还在，也不意味着我们喜欢在未来使用这个语言。当时的技术决策都是在当时的环境下产生的，在现在看来很扯的技术决策，有可能在当时是最好的技术决策。

对于一个优秀的团队来说，不存在一个人对所有的技术栈都擅长的情况——除非这个团队所从事的范围比较小。在一个复杂的系统里，每个人都负责系统的相应的一部分。尽管到目前为止并没有好的机会去构建自己的团队，但是也希望总有一天有这样的机会。在这样的团队里，只需要有一个人负责整个系统的架构。其中的人可以在自己擅长的层级里构建自己的架构。因此，让我们再回到我们的博客中去，现在我们已经决定使用动态的博客。然后呢？

作为一个博客我们至少有前后台，这样我们可能就需要两个开发人员。

![前后台](assets/article/chapter8/blog-basic.png)

（PS：当然，我们也可以使用React，但是在这里先让我们忽略掉这个框架，紧耦合会削弱系统的健壮性。）

接着，作为一个前端开发人员，我们还需要考虑的两个问题是：

1. **我们的博客系统是否是单页面应用？**。
2. **要不要做成响应式设计**。

第二个问题不需要和后台开发人员做沟通就可以做决定了。而第一个问题，我们则需要和后台开发人员做决定。单页面应用的天然优势就是：由于系统本身是解耦的，他与后台模板系统脱离。这样在我们更换前端或者后台的时候，我们都不需要去考虑使用何种技术——因为我们使用API作为接口。现在，我们决定做成单页面应用，那么我们就需要定义一个API。而在这时，我们就可以决定在前台使用何种框架：Angular.js、Backbone、Vue.js、jQuery，接着我们的架构可以进一步完善：

![含前端的架构](assets/article/chapter8/blog-with-frontend.png)

在这时，后台人员也可以自由地选择自己的框架、语言。后台开发人员只需要关注于生成一个RESTful API即可，而他也需要一个好的Model层来与数据库交付。

![含前端后台的架构](assets/article/chapter8/blog-with-fe-be.png)

现在，我们似乎已经完成了大部分的工作？我们还需要：

1. 部署到何处操作系统
2. 使用何处数据库
3. 如何部署
4. 如何去分析数据 
5. 如何做测试
6. 。。。

相信看完之前的章节，你也有了一定的经验了，你也可以成为一个架构师了。

###相关阅读资料

 -《程序员必读之软件架构》
 


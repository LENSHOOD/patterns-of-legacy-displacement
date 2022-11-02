# 过渡架构 Transitional Architecture
> 本文是对 [Ian Cartwright](https://www.linkedin.com/in/ian-cartwright-282952/)， [Rob Horn](https://www.linkedin.com/in/rob-horn)，和 [James Lewis](https://bovon.org/) 的文章 [*Transitional Architecture*](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-architecture.html) 的中文翻译。
>
> Thoughtworks 技术雷达中，对 Transitional Architecture 的介绍见[这里](https://www.thoughtworks.com/zh-cn/radar/techniques/transitional-architecture)。

*为了简化替换遗留系统所引入的软件单元，当替换完成后，我们会将其移除。*

---

替换老旧系统成功的核心是用新软件逐步取代旧软件，因为这样可以及早交付利益，并规避大爆炸的风险。在替换过程中，旧系统和新系统必须同时运行，以允许行为在新旧之间分离。此外，随着旧系统的消逝，两者之间的分工将定期发生变化。

为了实现新旧之间的互动，我们需要建立和演进 “过渡架构”，来支撑这种可能随着时间而变化的互动协作。而这种过渡性的集成结构可能在新系统的目标架构中并不存在。

或者更直接地说，你*将不得不*为那些会被丢弃的工作而投入。

## 怎么做

考虑一下翻新一栋建筑。一位建筑师已经为您提供了成品的效果图，而工人们正在准备开始。但第一步是在建筑工地搭脚手架。

租用脚手架并支付团队安装脚手架的费用是一项不可避免的投资。为了使关键工作得以完成，且在翻修期间降低工人的安全风险，这项投资是必须的。它甚至可以解锁新的可能——允许你在屋顶被替换的时候修理烟囱，或者修剪突出的树木（让比喻更进一步）。一旦工作完成，将会有另一个团队来拆除脚手架，你会很乐意看到它被拆掉。

在旧系统替换的上下文中，这个脚手架由软件组件组成，这些组件可以简化或支持向目标系统架构演进的过程。与脚手架一样，一旦达到了目标架构，就不再需要这些软件组件，必须将其删除。

一次性替换一个老旧的大型单体是有风险的，我们可以通过分几步替换来提高业务的安全性。可以采用 [价值流提取 Extract Value Stream](../patterns-for-breaking-up-the-problem/extract-value-stream.md)和 [产品线提取 Extract Product Lines](../patterns-for-breaking-up-the-problem/extract-product-lines.md) 等模式来实现将功能或将数据拆分为它们的子集。要做到这一点，我们必须将整个单体拆开，这就需要在单体引入 “接缝”，以将其拆解成碎片。为单体引入接缝的组件就是一种过渡架构，因为一旦单体被替换，它们必然会消失，单体也并不需要它们来履行现有的职责。

我们可以通过观察单体的不同部分如何相互通信，并在通信路径上放置一个组件来引入接缝，修改该组件就可以将流量转移或复制到其他部件中。例如 [事件拦截 Event Interception](./event-interception.md) 和 [抽象分支 Branch by Abstraction](https://martinfowler.com/bliki/BranchByAbstraction.html) 是分别用于事件和API通信的接缝。当我们创建这些接缝时，我们可以引入 [遗留模拟 Legacy Mimics](./legacy-mimic.md) 以向旧系统的通信流中引入新组件。

旧系统替换的最大挑战之一是处理旧系统经常直接访问的数据。如果可能的话，通过引入API来取代直接数据访问这种引入接缝的方法是明智的，比如采用 [仓库 Repository](https://martinfowler.com/eaaCatalog/repository.html) 模式。但当我们无法做到这一点时，我们需要复制系统的状态。一旦我们需要走这条路，[遗留模拟 Legacy Mimics](./legacy-mimic.md) 和 [事件拦截 Event Interception](./event-interception.md) 这两种方法都很有用。

<table class="use-case-list dark-head">
  <caption>过渡架构常用模式</caption>
  <thead>
    <tr><th>用于</th><th>模式</th></tr>
  </thead>
  <tbody>
    <tr>
      <td class="用于">创建解封</td>
      <td class="模式">
        <p>
          <a href="event-interception.html">事件拦截 Event Interception</a>
        </p>
        <p>
          <a href="legacy-mimic.html">遗留模拟 Legacy Mimic</a>
        </p>
        <p>
          <a href="https://martinfowler.com/bliki/BranchByAbstraction.html">抽象分支 Branch by Abstraction</a>
        </p>
        <p>
          <a href="https://martinfowler.com/eaaCatalog/repository.html">仓库 Repository</a>
        </p>
        <p>
          <a href="/bliki/DomainDrivenDesign.html">防腐层 Anti-Corruption Layer</a>
        </p>
      </td>
    </tr>
    <tr>
      <td class="用于">复制状态: new ➔ old <br>(“维持运转”)</td>
      <td class="模式">
        <p>
          <a href="legacy-mimic.html">遗留模拟 Legacy Mimic</a> (模拟服务消费)
        </p>
      </td>
    </tr>
    <tr>
      <td class="用于">复制状态: old ➔ new</td>
      <td class="模式">
        <p>
          <a href="legacy-mimic.html">Legacy Mimic</a> (模拟服务提供)
        </p>
        <p>
          <a href="event-interception.html">事件拦截 Event Interception</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>



即便是心中有一个明确的目标架构，也存在很多途径可以到达那里。团队可以走的每一条不同的路径都可以或需要由不同的过渡架构来实现。这时，我们就需要对每条路径进行成本/收益分析，这种分析需要足够细致到我们能够察觉它是否会对选择产生影响。

请记住，当不再被需要时就删除是使用过渡架构本身的一部分。在构建时也许值得多投入一些为了便于之后删除的功能。同样，我们需要确保它被正确删除——不必要的组件，即使未使用，也会使未来的团队在系统维护和演进的工作复杂化。



## 何时用它

没有人喜欢把辛勤的工作浪费掉，当我们谈论构建一些我们打算扔掉的东西时，这种情绪自然会产生。很容易得出结论，一次性的东西没有什么价值。但是，过渡架构在两个方面都提供了价值，这种价值应该与构建它的成本进行比较。

第一个价值是，它通常提高了向业务交付功能的速度。这里有一个趁手的比喻，就是在粉刷墙壁时，在边框上使用漆匠胶带（美纹纸）。在边框未被贴住的情况下，您必须在边框附近小心而缓慢地涂漆。在刷漆之前贴胶带和之后去除胶带的成本，将由为了避免上漆涂错的速度提升（和技能降低）来弥补。

软件中的这种权衡被时间价值的重要性放大了。如果企业需要一个新的仪表板，把要被替换的旧系统中现存的数据与新系统中的数据进行集成，那么您可以通过在新仪表板中构建一个网关，将旧数据读取并转换为新仪表板所需的格式，从而更快地实现目标。一旦旧系统被移除，这个网关将被丢弃，但是在替换发生之前有一个集成仪表板的价值可能远远超过构建它的成本。即使价值与成本相近，我们还应该考虑到更换旧版所需时间可能比预期的更长。

过渡架构的第二个价值是它可以如何降低遗留替换的风险。在一个客户管理系统中添加 [事件拦截 Event Interception](./event-interception.md) 将花费一定的成本，但一旦建立，就可以逐步迁移客户（例如使用 [价值流提取 Extract Value Stream](../patterns-for-breaking-up-the-problem/extract-value-stream.m) 或 [产品线提取 Extract Product Lines](../patterns-for-breaking-up-the-problem/extract-product-lines.md) )。迁移客户的一个子集可以减少迁移中出现严重错误的可能性，并可以减少任何问题所产生的影响。此外，如果出现真正严重的问题，[事件拦截 Event Interception](./event-interception.md) 使得很容易恢复到以前的状态。

通常，团队在替换遗留的过程中应该总是考虑过渡架构，并集思广益地讨论构建临时软件的不同方法来发挥过渡架构的好处。然后，团队应该评估增加的时间价值和降低的风险所带来的好处，对比构建这种短命软件的成本。我们认为许多人会惊讶于临时软件能如此频繁的偿还付出的成本。



## 案例：架构演进

本节探讨概述文章中介绍的中间件移除示例，并描述过渡架构如何实现系统的安全演进。

### 遗留结构

如概述所述，现有体系结构由主“Legacy 系统(Legacy System)“组成，该系统负责定价并通过一些“集成中间件(Integration Middleware)”将产品发布到”Legacy 店铺(Legacy Storefront)“。该中间件从”Legacy 队列(Legacy Q)“消费产品发布事件，并执行一个持续运行的流程来处理产品如何在商店上呈现。当产品售出时，”Legacy 店铺(Legacy Storefront)“会调用中间件，该中间件会更新底层共享”Legacy 数据库(Legacy Database)”中的产品状态。“Legacy 中间件(Legacy Middleware)”也将自己的内部状态存储在“Legacy 数据库(Legacy Database)”中，再通过数据仓库将其输入关键报表中。参见[关键聚合器 Critical Aggregator](./critical-aggregator.md)。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-legacy.png)



### 目标架构

在目标体系结构中，“Legacy 店铺(Legacy Storefront)”被保留了下来，但它的一些职责转移到了一个新的“店铺管理器(Storefront Manager)”组件中。当产品被路由到该渠道进行销售时，“店铺管理器(Storefront Manager)”会消费由”资产处置路由器(Asset Disposal Router)“生成的业务事件，并使用新的API将产品发布到“Legacy 店铺(Legacy Storefront)”上。“店铺管理器(Storefront Manager)”将负责产品在“Legacy 店铺(Legacy Storefront)”内的展示方式。当产品售出时，“Legacy 店铺(Legacy Storefront)”使用新的API调用“店铺管理器(Storefront Manager)”，然后“店铺管理器(Storefront Manager)”发出业务事件，以供下游”资产销售处理(Asset Sale Processing)“组件使用。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-target.png)



### 实现的第一个小步骤

要添加的第一个过渡架构是”事件路由(Event Router)“组件。它是 [事件拦截 Event Interception](./event-interception.md) 模式的一个示例。事件路由器创建了一个技术接缝，可以利用该接缝将产品通过新组件进行销售。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-1.png)



### 引入店铺管理器(Storefront Manager)

下一步是添加新的“店铺管理器(Storefront Manager)”。出于两个完全不同的目的，这里也要添加过渡架构。一个目的是为了将新组件与牵涉的遗留现状（例如数据结构和消息）隔开，另一个目的是为了维持遗留系统的运作。为了隔离（防腐层），创建一个”事件转换器(Event Transformer)“，用于将”事件路由(Event Router)“所转发的”Legacy 消息(Legacy Message)“转换为一种新的、干净的业务事件格式，交由“店铺管理器(Storefront Manager)”消费，且这部分将在目标架构中持续存在。“店铺管理器(Storefront Manager)”和“Legacy 店铺(Legacy Storefront)”将通过一个新的API进行协作，因此添加该 API 和内部的 [事件拦截 Event Interception](./event-interception.md)。这样，当产品售出时，“Legacy 店铺(Legacy Storefront)”会“回调”发布该产品的系统。为了维持遗留系统的运作，需要引入两种过渡架构。首先，当产品被售出的同时，新的业务事件会被发布出去。这些事件会被一个临时的“Legacy 数据库适配器(Legacy Database Adapter)”消费，该适配器模拟集成中间件，用销售信息更新”Legacy 数据库(Legacy Database)”。其次，创建一个“MI 数据模拟器(MI Data Mimic)”。它既是一个事件拦截器(Event Interceptor )，又是一个Legacy 模拟器(Legacy Mimic)——它在新API中拦截事件，并用业务关键报表所需的“状态”信息更新”Legacy 数据库(Legacy Database)”。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-2.png)



### 业务产出 - 停用遗留中间件(Legacy Middleware)

“Legacy 系统(Legacy System)“仍然负责确定哪些资产可以出售，并发送产品以供发布，但随着时间的推移，发送到新组件的产品数量持续增加(参见 [提取产品线 Extract Product Lines](../patterns-for-breaking-up-the-problem/extract-product-lines.md))，直到所有的流量都不再依赖“Legacy 中间件(Legacy Middleware)”。这个时候，“Legacy 中间件(Legacy Middleware)”就可以被移除了，而新的“店铺管理器(Storefront Manager)”和过渡架构组件则被留在生产环境。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-3.png)



### 引入资产处置路由器(Asset Disposal Router)

一段时间后，新的”资产处置路由器(Asset Disposal Router)“组件上线。（请记住，这个例子有些简化，并借鉴了一个更庞大的遗留置换方案的经验。）该组件发布产品的新业务事件以供“店铺管理器(Storefront Manager)”消费。”事件路由(Event Router)“和”事件转换器(Event Transformer)“都不再需要了，因为其他组件已经接管了决定哪些资产需要处置的相关工作，因此这些组件可以退役了。由于“Legacy 中间件(Legacy Middleware)”已经退役，业务关键报表已更改为使用来自新组件的数据（请参见[代码还原 Revert to Source](./revert-to-source.md))因此“MI 数据模拟器(MI Data Mimic)”组件也可以退役了。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-4.png)



### 安全抵达目标架构

不久后，新的“资产销售处理(Asset Sale Processing)”组件上线，它接管了“Legacy 系统(Legacy System)”的最后一组职责（在本示例的范围内）。到那时，最后一个过渡架构，“Legacy 数据库适配器(Legacy Database Adapter)”，也可以被删除了。“店铺管理器(Storefront Manager)”生成的业务事件由“资产销售处理(Asset Sale Processing)”组件使用。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-target.png)

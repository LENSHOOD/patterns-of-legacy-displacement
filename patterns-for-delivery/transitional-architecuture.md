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

租用脚手架本身并支付团队安装脚手架的费用是一项不可避免的投资。为了使关键工作得以完成，且在翻修期间降低工人的安全风险，这项投资是必须的。它甚至可以解锁新的可能——允许你在屋顶被替换的时候修理烟囱，或者修剪突出的树木（让比喻更进一步）。一旦工作完成，将会有另一个团队来拆除脚手架，你会很乐意看到它被拆掉。

在旧系统替换的上下文中，这个脚手架由软件组件组成，这些组件可以简化或支持向目标系统架构演进的过程。与脚手架一样，一旦达到了目标架构，就不再需要这些软件组件，必须将其删除。

一次性替换一个老旧的大型单体是有风险的，我们可以通过分几步替换来提高业务的安全性。可以采用 [价值流提取 Extract Value Stream](https://martinfowler.com/articles/patterns-legacy-displacement/extract-value-streams.html)和[产品线提取 Extract Product Lines](https://martinfowler.com/articles/patterns-legacy-displacement/extract-product-lines.html) 等模式来实现将功能或将数据拆分为它们的子集。要做到这一点，我们必须将整个单体拆开，这就需要在单体引入 “接缝”，以将其拆解成碎片。为单体引入接缝的组件就是一种过渡架构，因为一旦单体被替换，它们必然会消失，单体也并不需要它们来履行现有的职责。

我们可以通过观察单体的不同部分如何相互通信，并在通信路径上放置一个组件来引入接缝，修改该组件就可以将流量转移或复制到其他部件中。例如[事件拦截 Event Interception](https://martinfowler.com/articles/patterns-legacy-displacement/event-interception.html)和[抽象分支 Branch by Abstraction](https://martinfowler.com/bliki/BranchByAbstraction.html)是分别用于事件和API通信的接缝。当我们创建这些接缝时，我们可以引入[老旧模拟 Legacy Mimics](https://martinfowler.com/articles/patterns-legacy-displacement/legacy-mimic.html)以向旧系统的通信流中引入新组件。

旧系统替换的最大挑战之一是处理旧系统经常直接访问的数据。如果可能的话，通过引入API来取代直接数据访问来引入接缝是明智的，比如采用[仓库 Repository](https://martinfowler.com/eaaCatalog/repository.html)模式。但当我们无法做到这一点时，我们需要复制系统的状态。一旦我们需要走这条路，[老旧模拟 Legacy Mimics](https://martinfowler.com/articles/patterns-legacy-displacement/legacy-mimic.html)和[事件拦截 Event Interception](https://martinfowler.com/articles/patterns-legacy-displacement/event-interception.html) 这两种方法都很有用。

<table class="use-case-list dark-head">
  <caption>过渡架构常用模式</caption>
  <thead>
    <tr><th>用于</th><th>模式</th></tr>
  </thead>
  <tbody>
    <tr>
      <td class="用于">Creating Seams</td>
      <td class="模式">
        <p>
          <a href="event-interception.html">Event Interception</a>
        </p>
        <p>
          <a href="legacy-mimic.html">Legacy Mimic</a>
        </p>
        <p>
          <a href="https://martinfowler.com/bliki/BranchByAbstraction.html">Branch by Abstraction</a>
        </p>
        <p>
          <a href="https://martinfowler.com/eaaCatalog/repository.html">Repository</a>
        </p>
        <p>
          <a href="/bliki/DomainDrivenDesign.html">Anti-Corruption Layer</a>
        </p>
      </td>
    </tr>
    <tr>
      <td class="用于">Replicating state: new ➔ old <br>("Keeping the lights on")</td>
      <td class="模式">
        <p>
          <a href="legacy-mimic.html">Legacy Mimic</a> (Service Consuming Mimic)
        </p>
      </td>
    </tr>
    <tr>
      <td class="用于">Replicating state: old ➔ new</td>
      <td class="模式">
        <p>
          <a href="legacy-mimic.html">Legacy Mimic</a> (Service Providing Mimic)
        </p>
        <p>
          <a href="event-interception.html">Event Interception</a>
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

过渡架构的第二个价值是它可以如何降低遗留替换的风险。在一个客户管理系统中添加[事件拦截 Event Interception](https://martinfowler.com/articles/patterns-legacy-displacement/event-interception.html)将花费一定的成本，但一旦建立，就可以逐步迁移客户（例如使用[价值流提取 Extract Value Stream](https://martinfowler.com/articles/patterns-legacy-displacement/extract-value-streams.html)或[产品线提取 Extract Product Lines](https://martinfowler.com/articles/patterns-legacy-displacement/extract-product-lines.html) )。迁移客户的一个子集可以减少迁移中出现严重错误的可能性，并可以减少任何问题所产生的影响。此外，如果出现真正严重的问题，[事件拦截 Event Interception](https://martinfowler.com/articles/patterns-legacy-displacement/event-interception.html)使得很容易恢复到以前的状态。

通常，团队在替换遗留的过程中应该总是考虑过渡架构，并集思广益地讨论构建临时软件的不同方法来发挥过渡架构的好处。然后，团队应该评估增加的时间价值和降低的风险所带来的好处，对比构建这种短命软件的成本。我们认为许多人会惊讶于临时软件能如此频繁的偿还付出的成本。

## Example: Architecture Evolution

This section explores the Middleware removal example introduced within the overview article, and describes how Transitional Architecture enabled the safe evolution of the system.

### Legacy configuration

As described in the overview the as-is architecture consisted of the main Legacy system responsible for pricing and publishing products to the Legacy Storefront via some Integration Middleware. That middleware consumed product published events from a Legacy Queue and handled the long running orchestration of how the product was presented on the storefront. When the product is sold the Legacy Storefront calls the middleware which updates the products status within the underlying shared Legacy Database. The Legacy Middleware also stored its internal state within the Legacy Database which fed into critical reports via the data warehouse. See [Critical Aggregator](https://martinfowler.com/articles/patterns-legacy-displacement/critical-aggregator.html)

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-legacy.png)

### Target Architecture

Within the target architecture the Legacy Storefront remains, but has some of it's responsibilities moved into a new Storefront Manager component. The Storefront Manager will consume business Events produced by the Asset Disposal Router when a product gets routed to that channel for sale, and will publish the product onto the Storefront using a new API. The Storefront Manager will be responsible for how the product is displayed within the Storefront. When products are sold, the Legacy Storefront calls the Storefront manager using the new API which then emits a business Event to be consumed by a down stream Asset Sale Processing component.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-target.png)

### The first small enabling step

The first bit of Transitional Architecture to be added was the Event Router component. This is an example of the [Event Interception](https://martinfowler.com/articles/patterns-legacy-displacement/event-interception.html) pattern. The Event Router created a technical seam that could be exploited to route products for sale via new components.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-1.png)

### Introduction of the Storefront Manager

The next step was to add the new Storefront Manager. Transitional Architecture was also added here, that served two very different purposes. Namely to isolate the new components from legacy concerns (e.g. data structures and messages) and to keep the lights on within the legacy world. For isolation (Anti-corruption Layer) an Event Transformer was created to transform the Legacy Message being routed by the Event Router into a new and clean business event format to be consumed by the Storefront Manager, and that would endure within the target architecture. The Storefront Manager and Legacy Storefront would collaborate via a new API, so this was added, as well as internal [Event Interception](https://martinfowler.com/articles/patterns-legacy-displacement/event-interception.html) so that when a product was sold, the Legacy Storefront would "call back" to the system that published that product. To keep the lights on two bits of Transitional Architecture were required. Firstly when products were sold new business events were published. These were consumed by a temporary Legacy Database Adapter that mimicked the Integration Middleware, updating the Legacy Database with the sale information. Secondly the MI Data Mimic was created. This was both an Event Interceptor and a Legacy Mimic - it intercepted events within the new API and updated the Legacy Database with the "state" information required by the business critical reports.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-2.png)

### Business outcome - decommissioning of the Legacy Middleware

The Legacy System was still responsible for determining which assets could be sold, and sending products for publishing, but over time the number of products routed to the new components was increased (see [Extract Product Lines](https://martinfowler.com/articles/patterns-legacy-displacement/extract-product-lines.html)) until 100% of the traffic was being processed without reliance on the Legacy Middleware. At this point it was possible to decommission the Legacy Middleware, leaving the new Storefront Manager and Transitional Architecture components in production.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-3.png)

### Introduction of the Asset Disposal Router

After some time the new Asset Disposal Router component was brought on line. (Remembering that this example is somewhat simplified and drawn from the experiences of a much larger Legacy Displacement programme.) That component published the new business Events for products that could be consumed by the Storefront Manager. There was no longer a need for the Event Router as other components had taken over determining which assets were for disposal, nor the Event Transformer - so these components could be decommissioned. As the Legacy Middleware had been decommissioned the business critical reports had been changed to use data from the new components (see [Revert to Source](https://martinfowler.com/articles/patterns-legacy-displacement/revert-to-source.html)) and so the MI Data Mimic component could also be decommissioned.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-state-4.png)

### Safe arrival at the target architecture

Sometime later the new Asset Sale Processing component was brought online which took over the last set of responsibilities from the Legacy System (within scope of this example). At that time the last of the Transitional Architecture, the Legacy Database Adapter, could be removed. The business Events produced by the Storefront Manager were consumed by the Asset Sale Processing component.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-arch-target.png)

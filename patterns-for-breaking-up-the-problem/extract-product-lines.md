# Extract Product Lines

> 本文是对 [Ian Cartwright](https://www.linkedin.com/in/ian-cartwright-282952/)， [Rob Horn](https://www.linkedin.com/in/rob-horn)，和 [James Lewis](https://bovon.org/) 的文章 [*Extract Product Lines*](https://martinfowler.com/articles/patterns-legacy-displacement/extract-product-lines.html) 的中文翻译。
>

*通过产品线识别并分离系统*

---

许多应用程序旨在从同一物理系统中提供多个逻辑产品的服务。通常，这是由系统重用的愿望而驱动的。例如，“嗯，消费者贷款看起来与商业贷款非常相似” 或者 “衣服是一种产品，定制窗帘也是，它们有多大不同呢？” 我们遇到的一个主要问题是，表面上这些产品看起来相似，但在细节方面却非常不同。

随着时间的推移，为多个产品提供服务的单一系统可能会变得过于通用化，代码会不断发展以处理所有可能的产品组合。例如，对于一个通用系统，它被设计为处理 n 种产品，每种产品有 n 种变化，为了检查所有可能的组合而必须进行的测试，其工作量是 n 的阶乘。这是一个很快就会变得很大的数字。这也解释了为什么作者遇到的许多此类应用程序几乎没有自动化测试覆盖，而是依赖于庞大的，通常是手工的回归测试。因为根本无法测试这么多不同的代码路径。

> **一个与 ”条件“ 有关的例子**
>
> 在零售银行领域的另一个例子中，静态分析让我们发现了一个带有 61 个嵌套 if 语句的 Java 类。这使得 Daniel Terhorst-North 得出了这样的观察：并没有一个所谓少于多少个嵌套 if 语句的数量是合理的，因为如果你有一个if语句，很明显可以添加另一个，但如果你有60个呢，你还能做什么呢？尽管这个例子确实是一种病态的情况，但它确实说明了，如果我们不小心，某个为多个产品提供服务的单一代码库 - 在当前例子里是贷款 - 会演变成什么样子。

因此，问题通常是经济成本。开发和维护每个产品的系统可能比开发和维护单一系统更经济，这很难令人接受。通过按产品拆分，我们利用了多个产品可以同时进行更改的优势，并通过避免可能在不需要的地方引入缺陷的组合爆炸来降低风险。

当然，这是一种权衡 - 我们不希望为红裤子和黑裤子分别建立单独的应用程序，但我们可能希望为 ”现成的“ 和 ”定制的“、”家庭保险“ 和 ”宠物保险“ 分别建立应用程序。

正如[《提取价值流》(Extract Value Streams)](extract-value-stream.md) 中所述的，不同的产品线通常具有非常不同的价值流，。

## How It Works

**Identify the product or product lines within the system**. This will form the thin slice to be built / migrated. Look to identify all the capabilities that the existing system provides and map them to the new product. We tend to look through different lenses when identifying capabilites, data, process, users etc.

**确定系统中的产品或产品线**。这将构成要构建/迁移的薄片。在确定能力、数据、流程、用户等方面时，我们往往会采用不同的视角来寻找所有现有系统提供的能力，并将它们映射到新产品上。

**Identify shared capabilities**. Identify if the different product lines have BusinessCapabilities that are shared. There are several ways of approaching this, which we cover in UnderstandingYourBusinessCapabilities. Our advise as ever is to value Use over Reuse, so if in doubt try and limit the number of shared capabilites as much as possible

**确定共享能力**。确定不同的产品线是否具有共享的业务能力。有几种方法可以解决这个问题，我们在[了解您的业务能力](https://martinfowler.com/articles/patterns-legacy-displacement/understand-your-business-capabilities.html)中介绍了这些方法。我们的建议始终是强调使用而不是重用，因此如果有疑问，尽可能限制共享能力的数量。

**Choose who goes first**. Which product lines are you going to move off first? One approach we like is to think in terms of risk. After understanding the risk to the business of migration, we often like to take *the second riskiest product line*. This may feel counter-intuative and that we should take the least risky option first but actually we like to identify a product that is meaty enough to keep the business' attention and cause them to prioritise funding, but not so risky that the business will fail if there are problems.

**选择先行产品线**。你要先迁移哪个产品线？我们喜欢的一种方法是从风险角度考虑。在了解迁移对业务的风险后，我们通常会选择*第二个最有风险的产品线*。这可能感觉与直觉相反，认为我们应该先选择最不冒险的选择，但实际上我们希望找到一个足够重要的产品，以引起业务的关注，并导致他们优先考虑资金，但不会如有问题会导致业务失败。

**Identify your target software architecture**. It is very rare that we advise big bang replacements, which in this case would mean building out all the software for all of the products at once. Instead, look to identify the appropriate architecture for the thin slice identified in step 1.

**确定目标软件架构**。我们很少建议采用大爆炸替换，这在这种情况下意味着同时为所有产品构建所有软件。相反，寻找适合第1步中确定的薄片的适当架构。

**Identify your technical migration strategy**. As we discuss in the section on technology migration patterns, there are a number of different options that can be deployed depending on current constraints. If it's a simple web-application then it may be possible to use ForkByUrl. In other cases where ForkingOnIngress is appropriate it may be better to choose the MessageRouter pattern. Keep in mind that a TransitionalArchitecture may be necessary.

**确定技术迁移策略**。正如我们在技术迁移模式部分中所讨论的那样，根据当前的约束条件，可以部署许多不同的选项。如果它是一个简单的 Web 应用程序，那么可能可以使用ForkByUrl。在其他情况下，如ForkingOnIngress适用时，选择MessageRouter模式可能更好。请记住，可能需要过渡架构。

## When to Use It

You have a system with easily identifiable product lines that would benefit from:

你拥有一个系统，其中包含可以从以下因素中受益的易于识别的产品线：

1. Being worked on independently. Breaking up the system into separate products means that teams can be formed around the individual products allowing for progress to be made without the typical problems of change co-ordination including merge hell and long regression cycles.
1. 独立工作。将系统拆分为单独的产品意味着可以围绕各个产品组建团队，从而可以在不涉及变更协调问题（如合并问题和长时间的回归循环）的情况下取得进展。
2. That have different non-functional characteristics. You want to be able to offer different SLO's for each product. For example different load requirements for a given latency.
2. 具有不同的非功能性特征。你希望为每个产品提供不同的SLO。例如，对于给定的延迟，具有不同的负载要求。
3. Different rates of change. Some product lines are stable while other products are undergoing active development. Breaking up the system means that you do not risk changes to the stable products.
3. 具有不同的变更速率。有些产品线是稳定的，而其他产品则正在积极开发中。拆分系统意味着你不会冒稳定产品的变更风险。

## Insurance Example

In the insurance domain, different product types have very different characteristics. For example Vehicle insurance is typically high volume but low margin, whereas Home insurance is the opposite. It is also highly competative so the ability to make changes quickly is very important. One insurance company we worked with had developed a 3-tier architecture that served as the quoting engine for all the different products the insurer offered including Vehicle, Life, Home and Pet lines as shown in figure 1.

在保险领域，不同的产品类型具有非常不同的特点。例如，车辆保险通常是高交易量但低利润率的，而房屋保险则相反。这个领域竞争非常激烈，因此快速做出变化的能力非常重要。我们曾经与一家保险公司合作，他们开发了一个三层架构，作为他们提供的不同产品线（包括车辆、人寿、房屋和宠物险）的报价引擎，如图1所示。

![img](https://martinfowler.com/articles/patterns-legacy-displacement/insurance-app-arch.png)



> **The trouble with Tiers**
>
> **分层体系结构的问题**
>
> For many years it was most common to think in terms of the systems architecture first and the business or product architecture second. This has led to both a preponderonce of n-layer systems in the enterprise and also to many aging COTS products. Many systems build this way suffer from long lead-times for all changes as they must be coordinated across the n-layers.
>
> 多年来，最常见的思考方式是先考虑系统架构，其次是业务或产品架构。这导致企业中存在大量n层系统，并且许多老化的COTS产品。这种构建方式的系统通常需要长时间的协调过程，以便跨n层进行所有更改。
>
> In n-layer systems that serve multiple products, it's generally impossible to scale the system differently for each product line. It is very unusual for multiple products to have exactly the same non-functional characteristics. This isn't to say that layering is bad in general, in fact it's a [Good Thing for organising codebases](https://martinfowler.com/bliki/PresentationDomainDataLayering.html).
>
> 在为多个产品提供服务的n层系统中，通常不可能为每个产品线以不同方式扩展系统。很少有多个产品具有完全相同的非功能性特征。这并不意味着分层总是不好的，实际上它对于组织代码库是件好事。参见[Fowler博客](https://martinfowler.com/bliki/PresentationDomainDataLayering.html)。

## Understand the outcomes you want to achieve

The product owners for the business were increasingly frustrated at the lead time to change which was long and getting longer. They decided to get Thoughtworks in to take a look at their architecture and development processes to see if we could identify why things were so bad. The value stream map for the development process identified a number of constraints that were contributing to lead times exploding. While each technical domain was decoupled from the others, the different business domains were tightly coupled together. This meant adding a new requirement for the Vehicle product would often impact Home, Life and so on. These changes equired both careful thought and extensive regression testing before the usually fraught deployments. The multi-product architecture also imposed a limit on the number of people able to work on the codebase safely, further slowing progress.

业务的产品负责人越来越对变更的前置时间感到沮丧，这个时间变得越来越长。他们决定邀请Thoughtworks来审视他们的架构和开发过程，以确定问题的原因。该开发过程的价值流程图标识出了多个限制因素，这些因素导致前置时间的增加。虽然每个技术领域都与其他领域解耦，但不同的业务领域之间紧密耦合。这意味着为Vehicle产品添加新需求通常会影响到Home、Life等其他产品。这些更改需要仔细思考和广泛的回归测试，然后才能部署。多产品架构还对能够安全地在代码库上工作的人数设置了限制，进一步减缓了进展速度。

Finally, due to the volume requirements of the Vehicle product line and the growth of the business, the underlying data store had to be scaled often, requiring downtime to all the other products hosted by the system.

最后，由于Vehicle产品线的量要求和业务的增长，底层数据存储必须经常进行扩展，这需要停机时间来服务于系统中托管的所有其他产品。

## Decide how to break the problem up into smaller parts

As a result, the insurer decided to migrate away from their n-tier architecture organised around technical capabilities towards one organised by product lines. The products were already identified: Vehicle, Home, Life and Pet insurance. Once these were understood the different capabilities that they each needed were identified, for example the individual question sets, quoting and customer account as well as more technical capabilities such as Authentication and Authorisation. Customer Account was identified as a key shared capabilty that all of the product lines would use, a good example of adopting the Coordination approach from the EA Magic Quadrant which is described in more detail in [The One True Ring](https://martinfowler.com/articles/patterns-legacy-displacement/one-true-ring.html).

因此，保险公司决定将其围绕技术能力组织的n层架构迁移到以产品线组织的架构。产品已经确定：车辆、住宅、人寿和宠物保险。一旦这些得到了理解，便识别出了每个产品线所需的不同能力，例如个人问题集、报价和客户账户以及更多的技术能力，如身份验证和授权。客户账户被确定为所有产品线都将使用的关键共享能力，这是采用 EA 魔方图中描述的“协调”方法的一个很好的例子，更多细节请参见《The One True Ring》。

The next thing that needed to happen was to identify where to start. In order of revenue to the company, the product lines rated as Vehicle, Home, Life and Pet. Whilst in terms of customer numbers the order was reversed. As a result it was decided that Home insurance would be the first product line implemented separately. This balanced risk to the business of something going wrong with enough revenue to make the choice important.

接下来需要做的事情是确定从哪里开始。按公司收入的顺序，产品线的排名为车辆、住宅、人寿和宠物。而按客户数量排序则相反。因此，决定先实现独立的住宅保险产品线。这平衡了业务风险和重要性收益的关系。

## Successfully deliver the parts

![img](https://martinfowler.com/articles/patterns-legacy-displacement/insurance-event-driven.png)



> There is a lot going on in this picture, but in essence it's showing a very high level view of the interactions between a subset of the different capabilities needed to sell insurance to a customer.
>
> 这幅图展示了销售保险给客户所需的一些不同能力之间的互动关系。虽然涉及到很多细节，但其本质上是一个非常高层次的视图。
>
> 1. Once a customer has filled in the myriad questions required for insurers to quote and submitted their form (known as a Risk in the field), the Home Insurance application publishes a RequestQuote command.
> 1. 客户填写完保险报价所需的各种问题并提交其表格（在业界称为“风险”）后，住宅保险应用程序会发布一个RequestQuote命令。
> 2. The Quoting Engine, subscribed to a RabbitMQ topic, receices the RequestQuote command and does it's stuff (behind the scenes issues a number of internal and external requests to partners, brokers etc. In the case of this insurer, it involved sending requests of various types and using various protocols to over 40 other insurers. Honestly, if there was a standard for this, the whole thing would be a whole lot easier!
> 2. 订阅RabbitMQ主题的Quoting Engine收到RequestQuote命令并执行其任务（在幕后向各种内部和外部合作伙伴发出多个请求等）。对于这家保险公司而言，这涉及到向其他40多家保险公司发送不同类型和协议的请求。如果有一个标准，整个过程将会更加容易！
> 3. As replies from the downstream systems are received, QuoteReceived events are pulished onto another RabbitMQ topic by the Quoting Engine.
> 3. 随着下游系统的回复被接收，Quoting Engine会发布QuoteReceived事件到另一个RabbitMQ主题。
> 4. A number of things happen next since a number of capabilities in addition to the Home Insurance system are interested in the fact that a quote has been received. These include the Account Management capability and the Enterprise Data Warehouse (EDW). Fortunately these capabilities are also subscribed to the right Topic and can pick up these events as they need them.
> 4. 由于除了住宅保险系统外，还有许多其他能力也对已收到报价的事实感兴趣，因此会发生一些事情。这些包括Account Management能力和Enterprise Data Warehouse (EDW)。幸运的是，这些能力也订阅了正确的主题，并且可以根据需要获取这些事件。
> 5. A customer may then decide to proceed with one of the quotes to actually purchase a poicy and clickthough to do so. At this point a PolicyPurchased event is published and the Home Insurance system pats itself on the back in the knowledge of a job well done.
> 5. 然后，客户可能会决定选择其中一个报价来实际购买保险，并单击“继续”。此时，将发布PolicyPurchased事件，住宅保险系统会自豪地表扬自己，因为它已经成功完成了工作。
> 6. Finally, any other capabilities interested in the PolicyPurchased event receive it via RabbitMQ again and do their own thing (store it for reporting purposes in the case of the EDW and so that the Customer can easily retrieve it later in the case of Account Management.
> 6. 最后，任何对PolicyPurchased事件感兴趣的其他能力再次通过RabbitMQ接收它，并进行自己的操作（在EDW的情况下，将其存储以供报告目的，并在Account Management的情况下，使客户可以轻松检索它）。
>
> One nice aspect of using published business events in this way was the ease with which dependent systems such as as the data warehouse could be updated. In this case, the team created a simple adaptor over the EDW that turned the stuff thats happening in the new world into the star schema required by the old.
>
> 使用这种方式发布业务事件的一个好处是依赖系统（例如数据仓库）可以轻松更新。在这种情况下，团队创建了一个简单的适配器，将新世界中发生的事情转换为旧版所需的星型模式。

The above figure shows the event-driven architecture that the team started to build out. Communcation with the Quoting Engine and with the Customer Management capabilities was via events passed over a RabbitMQ message bus. These events were also propagated to the existing enterprise data warehouse for reporting purposes.

上面的图显示了团队开始构建的事件驱动架构。与报价引擎和客户管理功能的通信是通过在RabbitMQ消息总线上传递的事件进行的。这些事件也传播到现有的企业数据仓库用于报告目的。

As the team built out the new systems to the side of the existing one, preparations were put in place to cut over traffic from the legacy system to the new one. One downside to moving the product line in it's entirety was that the question set had to be implemented in full before customers could be switched over. Due to this contraint the new system went into a Beta phase where certain customers were offered the option to opt-in to use the beta version. Those that opted in also had the opportunity to provide feedback on the new look and feel. As the new system was progressively enhanced and the final cosmetic features were added, a go-nogo decision was taken and customers were gradually redirected to the new system over the course of several weeks. First one percent, then five, then ten percent etc. This allowed the team and business to grow in confidence that the new system was performing as expected, both from a functional and non-functional point of view. Finally, the new system was serving one hundred percent of traffic for Home Insurance. The team then turned to ongoing product development.

随着团队在旁边构建新系统，为将流量从旧系统切换到新系统做了准备。将整个产品线移动的一个缺点是必须完全实现问题集才能切换客户。由于这种限制，新系统进入了Beta阶段，某些客户被提供选择使用Beta版本的选项。选择参与的客户还有机会提供有关新外观和体验的反馈。随着新系统逐渐完善并添加最终的外观特征，做出了是否进行决策，逐步将客户重定向到新系统，这个过程持续了几个星期。首先是1％，然后是5％，然后是10％等等。这使得团队和业务方可以逐步增强对新系统的功能和非功能方面的信心。最后，新系统为家庭保险服务100％的流量。然后团队开始进行持续的产品开发。

## Change the organization to allow this to happen on an ongoing basis

After the success of the first migration, attention turned to the next challenge, moving Vehicle insurance using the same approach and the pattern was repeated until the migration was complete and the old system turned off.

在第一次迁移成功后，注意力转向下一个挑战，采用相同的方法移动车险，直到迁移完成并关闭旧系统。

Meanwhile, and gradually, the whole technical organisation moved away from a project-based approach to development to a product-centric one. This of course had teething problems. Product Ownership is a skill that needs to be built over time and so the transition was a gradual one. They also adopted the same approach to traditional IT Operations, and under the guidance of the CTO and Chief Architect moved towards a platform product engineering approach for on-demand infrastructure and then data and analytics.

与此同时，整个技术组织逐渐从以项目为基础的开发方法转向以产品为中心的方法。当然，这种转变有些困难。产品所有权是需要逐渐建立的技能。他们还采用了相同的方法来进行传统的IT运营，并在首席技术官和首席架构师的指导下，朝着按需基础架构和数据分析的平台产品工程方法发展。

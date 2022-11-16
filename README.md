# 遗留替换模式 Patterns of Legacy Displacement
> 本文是对 [Ian Cartwright](https://www.linkedin.com/in/ian-cartwright-282952/)， [Rob Horn](https://www.linkedin.com/in/rob-horn)，和 [James Lewis](https://bovon.org/) 的文章 [Patterns of Legacy Displacement](https://martinfowler.com/articles/patterns-legacy-displacement/) 的中文翻译

（副标题）对遗留软件系统的有效现代化

*当面临替换现有软件系统的需求时，组织通常会陷入一种 “半完成” 的技术替换的循环。经验教会了我们一系列模式，使我们能够打破这种循环，我们依靠的是：刻意的识别出替换遗留软件的预期结果是什么，将遗留软件替换的过程打散为各个部分，并逐步交付这些部分，同时改变组织文化，让组织认识到唯有变革才是不变的这一现实。*

---

在过去的几十年中，大部分时间我们都在帮助大型组织彻底改造其遗留系统。在这种过程中，我们领悟出了许多能真正奏效的途径，也看到了许多导致失败的途径。我们决定专门花一些时间，来记录我们从使用过的各种模式里所学到的内容。

本文可以充当这些模式的汇集。我们经常看到一些组织在遗留替换工作完成一半的情况下陷入困境。我们认为，打破这一循环的关键是完成四项活动，这四项活动会在在公司的生命周期中，按顺序甚至更多的是迭代往复的进行。我们以这些活动作为主要结构，来组织我们所描述的模式。

我们一直认为，有效的软件开发包括循序渐进的发布有价值的特性，我们认为写作也是如此，[尤其是在网络时代](https://martinfowler.com/bliki/EvolvingPublication.html)。我们以这篇叙述性文章为始，将在撰写它们的细节时逐渐添加各种模式，和展示它们如何组合起来的例子。我们无法承诺任何截稿日，因为我们的首要任务是客户工作，需要我们做的遗留替换工作还真不少。如果你有兴趣了解这部作品的更多部分，它们将在[Martin 的 Twitter feed](https://www.twitter.com/martinfowler)上，以及此网站的[RSS源](https://martinfowler.com/feed.atom)上发布。



## 遗留替换之踏车* The Legacy Replacement Treadmill

> *这里用循环往复的蹋车指代组织反复尝试遗留替换不得其法而所做的无用功

我们与许多曾多次尝试删除遗留系统的组织有过合作。在一个相当典型的组织中，他们会经历一系列3-5年的现代化计划。每次他们都会定义一种新的技术方法，然后将其作为一项大型的、持续多年的现代化计划的一部分。

每个计划都存在某个阶段，他们会遇到一个危机点，即不断变化的业务需求会赶超他们当前的技术战略，从而导致需要重新开始。为了应对该危机，一种情况是，他们的整个计划开始进行了瀑布式的“大爆炸”，这意味着先前大部分工作被丢弃了。另一种情况下，他们采取了更加增量式的交付手段，在已经很复杂的系统全景之上再添加一层稍微更新的技术。对于这两种情况，他们都无法除役任何遗留的系统栈，实现成本节约和风险降低的关键业务目标仍然没有实现，上述现象对于许多遗留替换工作来说是一个非常常见的结果。

有几个关键因素导致他们屡屡失败。

首先，他们看到的糟糕结果主要是组织的产物；具体的说，这涉及到领导力、组织结构以及工作方式。他们认为，只要选择更新的技术，而让其他东西或多或少保持不变，就会得到与过去不同的结果。事后看来，这显然是不现实的。

其次，现代化改造将由一个大型变革计划来实现，该计划本身包括一系列项目和团队。而这些项目被视为与任何BAU（日常工作）正交。因此，BAU 继续根据现有系统交付业务需求，而新项目团队则根据替换计划开始时商定的一组需求来交付。

随着时间的推移，他们看到业务的实际需要与计划开始时所签订内容之间的差距越来越大。变革计划运转的时间越长，其与 BAU 和未来需求之间的差距就越大。虽然为计划增加新需求的变更控制流程已经就位，但这些过程非常耗时，而且由于预先签订了供应商合同，变更成本也十分高昂。

某些失败的第三个关键因素是对现有的系统和业务流程[特性对等 Feature Parity](./patterns-for-breaking-up-the-problem/feature-parity.md)的渴望。这种尝试开始于他们承诺将以某种在幕后“改进”技术的方式，为企业提供他们今天所拥有的一切。在多次的失败经历结合对业务中断的担忧后，业务领导们认为这是一种风险较低的策略。这里的挑战在于哪怕是定义和同意与当前的“原样”功能，都是一项巨大的努力，它导致了一个大型单一“大爆炸”切换发布的计划。

我们从许多组织的观察中发现，技术最多只占遗留问题的50%，工作方式、组织结构和领导能力对成功同样重要。

## 打破循环 Breaking the cycle

显然，有必要打破“技术替代计划”的循环。简而言之，组织需要能够在满足业务需求的同时替换过时的技术，这一切都是在技术变革加速和竞争环境更加激烈的背景下进行的。

我们发现有一系列方法可以帮助应对这些挑战。它们能帮助解决如何将问题分解为更小部分的挑战，以便在改进技术的同时交付新的需求。一般来说，它们分为四类：

1. 明白你想要实现何种结果
2. 决定如何将问题分解成更小的部分
3. 成功交付部件
4. 进行组织变革，以使上述过程能持续发生

### 明白你想要实现何种结果

对于一个组织来说，在处理遗留时，至关重要的就是对他们想要实现的结果达成一致。虽然这看起来很明显，但一个组织的不同部分往往对期望的结果有着截然不同的看法。大多数遗留系统现代化的行动都涉及我们在下面列出的几个结果，但在踏上旅程之前，必须确定哪些是优先事项。

#### 降低变更成本

许多组织在决定处理遗留时的一个关键转折点是，所需的业务变更成本，可能是机会成本（又名延迟）或实施成本，开始远高于任何预期收益。一个早期预警信号是，需要花费数周或更多的时间来对一个网站进行更改，而这只会带来业务绩效的小幅增长。

这时，通常再也无法辩解称变更无法得到大的投资回报。换句话说，技术状况已经开始决定企业可以做出的变更的大小。对于许多组织来说，这意味着进行“BAU”变更与必须发起更大项目之间的区别。然后，这些大型项目会吸引所有以前不合理的小变化，从而增加其范围、成本和风险。

#### 改进业务流程

我们已经看到了许多业务流程紧随着遗留系统演化的例子，这些业务流程与系统 “由于自身约束而导致的” 工作方式紧密耦合，并且常常采用“系统外”的解决方案来构建人们为完成工作而遵循的业务流程。

我们看到一个使用“绿屏”终端的航空公司值机系统的例子，由于遗留系统的限制，值机流程必须遵循严格的顺序，这意味着更正或错误意味着重新开始值机流程。同样，最初该航空公司还没有提供转机航班，当增加这一功能时，由于该技术的限制，它必须在遗留系统中作为一个单独的工作流程来完成。因此，如果在办理登机手续时，乘客没有提及他们有转机航班，则会遵循错误的流程，包括打印错误的行李标签，只有在这之后，系统才会标记转机航班。通过改变流程，值机人员的工作和乘客的体验本可以大大改善，但遗留系统让这变得的不可能发生。

鉴于此，更新和更改业务流程却反过来要求更改支持性技术的工作方式也就不足为奇了。试图在不改变技术的情况下改变工作流程通常会导致“系统外”工作，即人们在将数据导入遗留系统之前，将数据提取到电子表格或类似表格中，在那里工作。

有一个组织中，整个库存订购流程实际上是在一个运行在团队经理PC上的Microsoft Access DB上完成的。由于遗留系统无法支持其供应商的新工作实践，他们感到很沮丧。他们每周会将数据导入和导出数次，与此同时，组织的其他成员将看到过时的数据，因为没有人意识到发生了什么。

这里值得注意的是，对替换系统需要支持数据导入导出功能的需求，其根因通常都源于上述这种变通。

#### 淘汰旧系统

旧系统需要被淘汰掉是遗留系统现代化的一个常见原因。这通常是由支持较旧的硬件或软件方面的挑战所驱使的，例如不断增加的支持成本以及硬件和软件的支持合同到期。

我们发现，从业务的角度来观察旧系统的淘汰是很有用的。因此，建立在旧技术基础上的系统本身并不足成为被替换的原因。相反，我们需要了解由于运行成本不断上升而产生的业务影响，或是缺乏对系统的支持或知识所带来的风险。

虽然一些组织确实做好了淘汰旧技术的计划，但许多组织似乎会忽略一些问题，直到到达危机点。反过来，这往往会推动组织走向现代化方法，这些方法看起来似乎能减少业务中断或是快速达成目的，但这通常是反模式的，我们稍后将描述其中的一些陷阱。

多年来，我们对许多大型组织使用不受支持的硬件和软件运营业务感到震惊，在eBay上购买备件是一个令人惊讶的常见故事。如果你有遗留技术，那么做一个适当的调查并创建一个日历来记录各种生命周期结束的支持日期是非常值得的。

尽管许多组织将旧系统的退役作为遗留系统现代化的一个关键结果，但并不少见的是，这种情况并未实际发生，遗留系统最终仍在使用，相关的业务目标仍未实现。

#### 迫在眉睫的中断

对于一些组织来说，由于外部因素（如监管变化、新的初创竞争对手或现有竞争对手的重大变化），解决遗留问题的实际临界点可能会出现。通常，当面临“必须要做”的改变时，很明显，做出反应所需的资金和时间就变得大多了。

外部事件向组织的领导层表明，他们不再有能力按比例成本（Proportionate Cost）做出改变了。

#### 更新的技术

采用新技术不应该成为遗留系统现代化的原因，仅仅为了技术本身而拥有新技术很少会成为任何组织的关键目标。相反，它应该以最符合企业当前和未来需求的方式进行选取和选择。这里的一个挑战是，技术变革的步伐正在加快，技术的“有用”寿命正在缩短。“有用”的实际定义取决于组织，但一般来说，我们需要考虑以下事项：

- 竞争优势
- 匹配竞争对手或市场产品
- 加快变革步伐
- 更便宜的变更成本
- 更低的运行成本

我们今天做出的关于最佳和最有用技术的选择很可能会在相对较短的时间内被更好的替代方案所取代。这使得在寻找满足未来需求的技术方面做出正确的决定具有潜在的风险。

这里的一个好方法是不要做出任何2-3年内无法轻易“完成”的选择。这对技术选择以及总体设计和方法都有影响。当我们意识到这种加速的变变革步伐时，选择一个具有5-10年回报时间的大型平台是很难证明其正确性的。

> 我们想要像 Netflix 一样
>
> 我们多次看到的一个问题是所谓 “Netflix嫉妒”。即一个组织的技术领导者专注于像 Netflix 或其他一些成功的大型科技公司那样。这意味着他们试图模仿其工作方式或选择相同的技术解决方案。虽然如果他们也从事流媒体电影业务，这可能是合适的，但这往往会导致选择不合适的技术。这些技术通常具有可扩展的能力，但也具有更高的复杂性和成本，这是大多数企业所不需要的。

### 决定如何将问题分解成更小的部分

从广义上讲，这涉及到在当前的业务和技术架构中找到合适的“接缝”。重要的是，你必须考虑当前解决方案的元素如何映射到不同的业务能力上。对于遗留系统，这通常意味着探索一个大型技术解决方案如何满足多个业务需求，然后看看是否可以提取出单一需求并使用新的解决方案独立交付。理想情况下，这应该在相互依赖最小的情况下交付。

一个普遍的反对意见是，寻找这些接缝太难了。虽然最初我们同意这是一个挑战，但我们发现它比那些经常导致特性对等(Feature Parity)和大爆炸(Big Bang)发布的替代方案更好。我们还观察到，许多组织排除了这种方法，因为他们孤立地看待技术或业务流程。仅更改技术的一部分，或仅独立更新一个业务流程都可能失败，但如果我们可以考虑并同时实现这两者，就有办法 “吃掉大象”([Eat the Elephant](https://spicerfacilitation.ca/eating-an-elephant-one-bite-at-a-time/))。

#### 开始分解

遗留系统现代化在旅程开始时似乎是一个最令人畏惧的命题。像任何旅程一样，我们必须首先尝试并理解最初的方向在哪里。而且，就像所有的旅程一样，你必须从你当下所处的位置开始。我们遇到的一个常见问题是，我们似乎常常从一片森林开始，看不到前方的全景，因此也不知道该走什么方向。那么，第一步就是爬上一棵树，好好看看四周！这意味着在最短的时间内尽可能地了解当前的系统和架构。这通常是非常难做到的，而且很容易陷入太多细节中。

幸运的是，有许多真正有用的工具可以协同使用，来获得足够好的理解从而让我们得以继续。这些工具将在其他地方详细讨论，但如下摘要列表将包括[事件风暴 Event Storming](./patterns-for-understanding-the-problem/event-storm.md)，[Wardley 映射 Wardley Mapping](https://blog.gardeviance.org/2015/02/an-introduction-to-wardley-value-chain.html)、业务能力映射(Business Capability Mappin)和域映射(Domain Mapping)。请注意，在本列表中，我们主要关注的是业务概念如何映射到系统架构中，进而了解该[架构如何支持价值生成](https://martinfowler.com/articles/value-architectural-attribute.html)。 这是一个经常缺失的视图，尤其是对于遗留系统。

<table class="dark-head">
<caption>理解问题的模式</caption>
<tbody><tr><td><a href="./patterns-for-understanding-the-problem/identify-business-capabilities.md">识别业务能力 Identify Business Capabilities</a>&nbsp;†</td><td>识别组织中稳定的部分，以构建团队和软件</td></tr>
<tr><td><a href="./patterns-for-understanding-the-problem/create-town-plan.md">创建城市规划 Create Town Plan</a>&nbsp;†</td><td>识别组织中稳定的部分，以构建团队和软件</td></tr>
<tr><td><a href="./patterns-for-understanding-the-problem/value-stream-map.md">价值流图 Value Stream Map</a>&nbsp;†</td><td>描述用户如何完成其工作的工件</td></tr>
<tr><td><a href="./patterns-for-understanding-the-problem/event-storming.md">事件风暴 Event Storm</a>&nbsp;†</td><td>用于理解业务流程的技术</td></tr>
</tbody></table>

† 目前仅预留桩

>事件风暴-现代流程图的瑞士军刀
>
> 关于这项技术已经有很多文章，作者发现它是一个非常通用的工具，可以在多种环境中使用。事实上，除了绘制业务流程和领域之外，作者还将其用于价值流映射和可视化生产路径。

具体地说，我们发现人们经常在遗留系统的边界停止探索，好像 “恶龙来了”，便不再继续。如果不跨越边界，不去揭露遗留系统如何支持（或阻碍）业务流程和活动，则很难找到并提取要交付的切片（分解更小的部分）。

另一个经常被忽视且非常有价值的信息来源是系统本身的用户。事实上，在作者的经验中，这通常是你可以发现惊人数量的有用信息的地方，尤其是暴露了通常围绕旧系统构建的许多变通方法和影子IT生态系统，即*实际*运行业务的 Access 数据库和版本化的 Excel 电子表格。客户旅程图(Customer Journey Mapping)、创建服务蓝图(Service Blueprints)和价值流图(Value Stream Mapping )是用于显示此类细节的良好工具。

<table class="dark-head">
<caption>拆解问题的模式</caption>
<tbody><tr><td><a href="./patterns-for-breaking-up-the-problem/extract-product-lines.md">产品线提取 Extract Product Lines</a></td><td>按产品线识别和分割系统</td></tr>
<tr><td><a href="./patterns-for-breaking-up-the-problem/extract-value-streams.md">价值流提取 Extract Value Streams</a>&nbsp;†</td><td>按价值流识别和分割系统</td></tr>
<tr><td><a href="./patterns-for-breaking-up-the-problem/feature-parity.md">特性对等 Feature Parity</a></td><td>
使用新的技术栈复制一个遗留系统的现有功能</td></tr>
<tr><td><a href="./patterns-for-breaking-up-the-problem/one-true-ring.md">至尊魔戒 The One True Ring</a>&nbsp;†</td><td>通过识别独特和共享的业务能力来细分问题</td></tr>
</tbody></table>


† 目前仅预留桩

### 成功交付部件

对更快变化的需要，以及增量式交付和不涉及大的依赖的独立变更业务元素的能力，往往会通向 "敏捷 "交付方法和基于微服务的架构。持续交付成为这些可单独部署的组件的必备条件。这不仅仅是一个普通的软件交付的挑战，而是要找到从现有的大型解决方案的元素切入、与之共存并最终替代的策略。有几种成功的策略，包括并行运行(parallel run)、入口分叉(fork on ingress)和流量转移(diversion of flow)。

<table class="dark-head">
<caption>交付模式</caption>
<tbody><tr><td><a href="./patterns-for-delivery/critical-aggregator.md">关键聚合器 Critical Aggregator</a></td><td>组合来自企业不同部门的数据，以支持做出关键决策</td></tr>
<tr><td><a href="./patterns-for-delivery/canary-release.md">金丝雀发布 Canary Release</a>&nbsp;†</td><td>在一部分用户中推送变更</td></tr>
<tr><td><a href="./patterns-for-delivery/stop-the-world.md">停止世界式切入 Stop the World cutover</a>&nbsp;†</td><td>暂停正常的商业活动，同时切入新的系统</td></tr>
<tr><td><a href="./patterns-for-delivery/revert-to-source.md">回归本源 Revert to Source</a></td><td>识别数据的本源，并整合到其中</td></tr>
<tr><td><a href="./patterns-for-delivery/transitional-architecture.md">过渡架构 Transitional Architecture</a></td><td>为方便替换遗留系统而安装的软件元素，我们打算在替换完成后将其移除</td></tr>
<tr><td><a href="./patterns-for-delivery/divert-the-flow.md">流量转移 Divert the Flow</a></td><td>首先将跨组织的活动从遗留问题中转移出来</td></tr>
<tr><td><a href="./patterns-for-delivery/dark-launching.md">暗中启动 Dark Launching</a>&nbsp;†</td><td>在不使用其结果的情况下调用一个新的后端特性，以评估其性能影响</td></tr>
<tr><td><a href="./patterns-for-delivery/legacy-mimic.md">遗留模拟 Legacy Mimic</a></td><td>新系统与遗留系统以这种方式互动，使旧系统察觉不到任何变化</td></tr>
<tr><td><a href="./patterns-for-delivery/event-interception.md">事件拦截 Event Interception</a>&nbsp;†</td><td>拦截对系统状态的任何更新，并将其中的一些更新发送到一个新的组件上</td></tr>
</tbody></table>

† 目前仅预留桩

### 进行组织变革，以使上述过程能持续发生

如果我们退一步，看看交付新业务需求的整个过程，我们很快就会发现技术问题只是一部分。如果我们使用较新的技术来减少建立解决方案的时间和成本，那么我们就能突显任何问题，包括围绕如何对需求达成一致以及如何将变化投入生产等。

我们需要组织结构和流程的改变，以充分利用更好的技术，根据[康威定律](https://martinfowler.com/bliki/ConwaysLaw.html)，我们还需要一个有利于此的技术架构。如果团队和他们的沟通是围绕着现有的遗留解决方案和流程组织的，我们可能需要使用[反康威法则](https://martinfowler.com/bliki/ConwaysLaw.html#icm)对他们进行重组，以匹配新的解决方案和它的架构。

遗留系统会制约和限制采用更多现代工程实践的能力，特别是那些与极限编程(eXtreme Programming)和持续交付(Continuous Delivery)有关的实践。在替换遗留系统时，重要的是要确保工作方式的改变，以确保我们最终不会回到一个变更起来缓慢、困难和昂贵的系统。

遗留问题也是组织文化和领导力的产物，如果没有更广泛的变革，你应期待得到与以前一样的结果。我们已经观察到许多遗留系统现代化的努力由于 "公司抗体" 而失败，这些抗体发现了新发生的事物，并采取行动将其从组织中剔除。

仅举一个例子，说明一个常见的组织可以拒绝变革的方式；我们与一家非常大的电信公司合作，该公司希望为移动电话构建软件。领导层都明白，比起他们看到的专注于固定基础设施的现有项目，这意味着更快的反馈周期和更频繁的变化。

虽然领导层明白这一点，但并没有对现有的工作实践或管理这些流程的中层管理人员做出改变。因此，现有的变更控制流程被严格地应用。最后，软件团队花在填写变更控制表和参加变更控制会议的时间比他们生产软件的时间还要多。"公司抗体" 成功地拒绝了新的工作方式。

组织变革是一个很大的话题，已经有很多文献资料，遗留问题的关键挑战往往与时间有关。很少有组织能够负担得起在重新设计（或重建，对于外包商的受害者）他们的整个交付方式以及他们的组织结构和关键业务流程并延后遗留系统现代化的进程。虽然组织转型这个更广泛的话题超出了我们的范围，但我们确实推荐了一些策略来应用和保护在遗留问题上的新工作方式。如果你只改变遗留，而不做其他事情，那么可以预期你将在几年后再次进行遗留替换。

<table class="dark-head">
<caption>持续组织变革的模式</caption>
<tbody><tr><td><a href="./patterns-for-ongoing-organisational-change/build-as-you-mean-to-continue.md">按你的意思持续建设 Build as you mean to continue</a>&nbsp;†</td><td>以你需要的方式创建你的遗留替换，一旦它上线，就会持续下去</td></tr>
<tr><td><a href="./patterns-for-ongoing-organisational-change/protected-pilot.md">试点保护 Protected Pilot</a>&nbsp;†</td><td>为新工作创建一个试点计划，并将其从正常的公司治理过程中分离出来</td></tr>
<tr><td><a href="./patterns-for-ongoing-organisational-change/new-co.md">新公司 New Co</a>&nbsp;†</td><td>组建一个全新的公司来寻求市场颠覆</td></tr>
</tbody></table>

† 目前仅预留桩

肯定还有其他的战略和方法来实现组织转型，我们只是强调了这两个，因为在某种程度上，它们可以让遗留系统现代化的工作尽早开始，而不是拖延。

> 最难的转变是范式转变(Paradigm Shift)
>
在传奇的管理顾问伊莱-戈德拉特（Eli Goldratt）发表["目标"](https://en.wikipedia.org/wiki/The_Goal_(novel))的20年后，他参加了《财富-小型企业》(Fortune Small Business) 的采访。当被问到为什么这么多组织变革缓慢时，他回答道，大多数人都会想方设法避免像约束理论(Theory of Constraints)这样的根本性变革，他们会做任何事情来避免范式的转变。

>他之后建议，为了成功改变范式，需要做三件事。
>
>1. 必须有真正的压力来改善结果
>2. 同一范式内的其他一切都已经被尝试过了，而且，
>3. 他们在迈出第一步时得到了帮助



## 案例：移除集成中间件 An example: Integration Middleware Removal

这个例子描述了我们的一个团队是如何使用一些遗留系统现代化模式来成功替换对客户业务运作至关重要的集成中间件，该例本身也是一个更大的遗留系统现代化计划的一部分。他们将模式和重构结合起来，成功地管理了业务的风险，并促进了“消化这头大象中最粗糙部分”的过程。

### 明白想要实现的结果

我们的团队所面临的挑战是如何将一个已经失去支持、难以变更和非常昂贵的集成中间件，替换为一个新的可受支持的、灵活的业务解决方案。且不破坏现有业务运作或将其置于风险之中。有问题的中间件是用来整合后端系统和店面系统的。这些系统共同负责每天销售价值数千万英镑的高价值独特产品。

这项工作是一个更大的计划中的高度优先部分。整个支持业务的后台系统正在被替换，店面系统也将在几年内进行现代化改造。

因此，按照上面的步骤1，团队需要实现的业务成果被定义为：

1. 改进业务流程
2. 怎么做？这个特殊的集成中间件解决方案包含了大量的逻辑，包括业务的核心规则，比如在哪个渠道销售产品，或者如何以及何时在店面内展示产品进行销售。这个现有的系统很难改变，扼杀了业务创新，而且逻辑上的缺陷导致了一些问题，比如有一段时间产品甚至没有在销售！

3. 尽快淘汰旧系统
4. 为什么？为了减少现有的（和不断增加的）许可证和支持成本。此外，要减少在过时的中间件和数据库技术上运行关键功能所带来的业务风险。



![Integration Middleware Removal Example](https://martinfowler.com/articles/patterns-legacy-displacement/IntegrationMiddleWareRemoval_1.png)



高阶的系统处理流程：用户使用遗留后台系统中的屏幕单独管理产品的定价和发布。对于每个发布的产品，该系统会将一条消息放到SwiftMQ 队列中。集成中间件将消费该消息，创建它自己的产品状态视图，并调用店面系统的遗留 SOAP API来发布它。随着时间的推移，集成中间件将使用 API 更新产品的状态，以改变产品提供给客户的方式（例如，将产品从 "仅预览 "改为 "新提供 "等）。当客户购买产品时，遗留店面系统将调用集成中间件提供的 API。中间件将更新自己的产品状态，并将销售信息更新到遗留系统的主数据库里。

### 将问题分解成更小的部分：第一个接缝和第一次重构

在 [Inception](https://martinfowler.com/articles/lean-inception/) 期间，团队与对遗留系统有深入了解的人进行了一次研讨会，以合作的方式将现有的和未来的软件架构可视化。这样做之后，他们发现了一个可以利用的技术接缝，那就是在遗留后端和集成中间件之间基于消息的集成。遗留后端是一个老旧的 J2EE 应用程序，它将 "发布产品 "的消息放到一个由非常旧的 SwiftMQ 版本提供的队列中。[事件拦截 Event Interception](./patterns-for-delivery/event-interception.md) 模式在这里很有用，如果作为[基于内容的路由器](http://www.enterpriseintegrationpatterns.com/ContentBasedRouter.html)来实现，就可以控制来自遗留后端的消息如何被路由，并创建一个选项，使消息可以被路由到新系统。

集成中间件也处理来自店面系统的消息（如产品销售），使用 JDBC 直接更新遗留后端背后的主数据库的状态。通过 SwiftMQ 的异步消息传递和 JDBC 的数据库更新共同构成了遗留后端和集成中间件之间的接口。

![Branch by abstraction](https://martinfowler.com/articles/patterns-legacy-displacement/BranchByAbstraction.png)



虽然当时没有发现，但该团队能够使用[抽象分支 Branch by Abstraction](https://martinfowler.com/bliki/BranchByAbstraction.html)模式，在一个子系统的规模上，作为策略来实现对传统中间件的替换。抽象层是队列和 JDBC。只要确保新的实现遵守该抽象层，那么它可以在不影响业务运营的情况下被替换回"有缺陷的提供方"。

团队做的第一件事是通过重构添加了一个事件路由器(Event Router) 来实现事件拦截。

![(P)Refactoring to add Event Interceptor](https://martinfowler.com/articles/patterns-legacy-displacement/IntegrationMiddleWareRemoval_2.png)



事件路由器(1)的创建有三个主要功能：

高阶的系统处理流程：这里选择了[重构](https://martinfowler.com/books/refactoring.html)这个术语，因为系统的结构被改变了，但行为却没有任何可观察到的变化。现在，当用户发布产品时，遗留后端系统仍然将消息发布到 SwiftMQ 队列中。现在，该消息不是由集成中间件来消费它，而是由事件路由器从该队列中消费消息，并将其原封不动地送入另一个 SwiftMQ 队列中。集成中间件从这个替代的队列中消费消息，这一变化可以通过一个简单的配置设置实现。

1. 从一个 SwiftMQ 队列中取出消息，并将其送入另一个 SwiftMQ 队列(2)。通过对一些配置的微不足道的改变，集成中间件就可以从这个新队列(2) 中消费消息。
2. 总的来说，重构使可观察的系统行为没有发生变化，但事件路由器现在是过渡架构(Transitional Architecture) 的一部分，已经被插入到消息处理流水线中了。
3. 对事件路由器的设想是，通过配置，将消息路由到另一个目的地 - 使新的实现能够处理发布消息。[事件拦截 Event Interception](./patterns-for-delivery/event-interception.md)
4. 事件路由器还将作为桥梁，提供从旧的 SwiftMQ 技术到为目标架构选择的新的 ActiveMQ 技术的对接。

实现事件路由器并不像它本来那样简单。由于缺乏可用的驱动/库，与 SwiftMQ 的集成是有问题的，而且这种方法受到了多次挑战。团队理解这种方法的价值，完成了这项工作并发布到生产中。他们在外部监控了新的组件，并准备使用新的[持续交付](https://martinfowler.com/bliki/ContinuousDelivery.html)流水线来逐步增强其能力。

### Successfully deliver the parts: building out the functionality, maintaining the contract

![New Implementation and Rollout](https://martinfowler.com/articles/patterns-legacy-displacement/IntegrationMiddleWareRemoval_3.png)



The new Store Front Manager(1) was now iteratively built out by the team . Relevant to this example, that build included the Master Database Adapter (2) implementing the Legacy Mimic pattern. This was required as part of the abstraction layer, to update the Master Database with sales information received from the Store Front. As the Event Router did not transform messages, a Legacy Event Adapter (3) ([Message Translator](http://www.enterpriseintegrationpatterns.com/MessageTranslator.html)) was created to transform messages into a new format, not exposing the legacy world to the new, and aligning to the principles of the new architecture. The Legacy Storefront [Adapter](https://martinfowler.com/bliki/RequiredInterface.html)(4) was also implemented between the new Store Front Manager(1) and the Legacy Store Front to isolate the new implementation from future changes that would be coming when the store front was replaced.

A new API was introduced on the Legacy Store Front (5) that the new Store Front Manager was to use. Additionally, a feature was added enabling call backs for products published on the new API to be sent to the new Store Front Manager's adapter (4). Critically, this enabled the legacy implementation and the new implementation to be run in parallel.

### Successfully deliver the parts (cont.): transitioning into live service - using a second seam

With all of the pieces in place the business were able to test the new solution, but how to roll it out into live service **in a risk managed way**.

To do this they took advantage of another seam - this time using the Segment by Product pattern. The Event Router was enhanced to add configurable routing (6) by product type as well as by unique product IDs. The team were able to test the publishing, management and sale of individual products by ID, and then over time configure the router with progressively more and more product types, essentially increasing the percentage of products handled by the new solution.

When all products were being handled by the new systems, the legacy Integration Middleware was decommissioned, realising the significant £ saving in license, support and datacenter hosting fees.

![Legacy Gone](https://martinfowler.com/articles/patterns-legacy-displacement/IntegrationMiddleWareRemoval_5.png)



High level system processing: Unless the business had specified otherwise, processing of a given product proceeded as before. For products where the business were happy to route them through the new system the processing was now as follows. A publish message was placed on a SwiftMQ queue. The event router would check the message payload and inspect the product manufacturer. If configured the Event Router would place this message unaltered onto an ActiveMQ queue. The Legacy Event Adapter transformed the message into a business event aligned to principles from the target architecture. The New Storefront Manager application would store its own representation of the product and forward a command message via a queue for the product to be published. The Legacy Storefront Adapter consumes that command, and calls the new v2 API on the Legacy Storefront.

As required by the business, users can now manipulate how the products are presented on the storefront in addition to the manager changing this overtime by issuing new commands.

When a product (published via the v2 API) is sold, the Legacy Store Front calls back to an API provided by the Legacy Storefront Adapter, which transforms the call into a business event and places that event onto an ActiveMQ queue. The New Storefront Manager, and Master Database Adapter consume these events. The new storefront manager updating its internal state of the product, and the Master Database Adapter updating the legacy Master Database with the sale information.

### Changing the organisation to allow this to happen on an ongoing basis

Our teams had already been working with the client, in another part of the organisation and had already successfully displaced a different legacy system.

At an engineering level across the organisation continuous delivery and good supporting quality practices were now the established norm, and a microservices style architecture enabled regular and independent deployment of containerised services onto a cloud based platform.

The teams on the new programme, working with new stakeholders, needed to take this other part of the business on the same "agile and CD" journey, and early risk managed releases enabled trust to be earned. Over time it was possible to demonstrate how new engineering and quality practices including CD were mitigating the same risks that had historically resulted in higher levels of bureaucracy and governance. So less frequent, larger scope releases were also displaced by smaller, more frequent, higher confidence deployments, and toggled releases to the business when they were ready to take on the changes.

### Closing thoughts

Of course there was significantly more complexity and integration requirements than implied by the simplified story above. An example of the need for Archeology introduced itself shortly after testing the new implementation in production. A number of business critical management information reports did not tally - products were "getting lost".

After much digging the team found that the database used by the Integration Middleware (for storing the state of long running business transactions) was replicated to the organisation's data warehouse. Via a number batch jobs, stored procedures and views this data was made available for use within the business critical KPI reports.

![LegacyModernizationExample_Archeology](https://martinfowler.com/articles/patterns-legacy-displacement/LegacyModernisationExample_Archeology.png)



Additional Legacy mimics were required to ensure that these reports did not break. The team used a [Wire Tap](http://www.enterpriseintegrationpatterns.com/WireTap.html) on sales messages coming from the Store Front and using JDBC injected the data into appropriate tables within the data warehouse. These additional mimics also became part of the transitional architecture, and would be removed when possible.

The approach of branch by abstraction, and use of patterns and practices described above was one intended to lower risk.

Using Event Interception (technical seam), Legacy Mimics and Transitional Architecture enabled the client to break the problem up. Then segmenting by product (business seam), in this case product type, enabled fine control of the wider rollout and further management of risk. Overall the approach allowed the business to proceed with the system replacement at the pace that was comfortable to them.

The approach allowed risk to be managed, but came at a cost. A question to consider is therefore "What value does the business place on this risk mitigation?" Being explicit and quantifying it, will allow a team to track investments against it. The event router and legacy mimics were part of an investment in a transitional architecture intended to manage risk. Their roles were to create options enabling risk to be managed. It can be very easy for such work to be seen as "throw away" - and as such a cost to be avoided wherever possible. Be explicit and transparent in this "value of risk mitigation" vs "cost of transitional architecture" trade-off.

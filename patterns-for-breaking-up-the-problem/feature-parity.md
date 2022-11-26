# Feature Parity

> 本文是对 [Ian Cartwright](https://www.linkedin.com/in/ian-cartwright-282952/)， [Rob Horn](https://www.linkedin.com/in/rob-horn)，和 [James Lewis](https://bovon.org/) 的文章 [*Feature Parity*](https://martinfowler.com/articles/patterns-legacy-displacement/feature-parity.html) 的中文翻译。

*使用新的技术栈复制遗留系统的现有功能*。

---

在许多情况下，当我们发现自己与IT主管交谈时，我们听到他们有一套老旧的应用程序，构建在快要、甚至已经消亡的技术上。更多的时候，这些系统被托管在昂贵的数据中心，由第三方管理，并签订了不灵活的合同。这些应用程序对企业的成功运作至关重要，与此同时，它们也是业务和运营风险的最大来源之一。

他们都很清楚，是有机会对其进行改进、优化流程并释放新机会的。然而，要完全做到这一点将是破坏性的，并存在诸多依赖。例如，现有的 "日常工作" 的承诺，其他的变革计划，以及末端用户工作的部门其现有的计划和预算。

在这种情况下，一种方法是试图通过 "简单" 地技术替换而使其他一切 "保持原样" 来尽量减少替换对更大范围组织的影响。这种方法通常被称为 "特性对等（Feature Parity）"，或者被那些尝试过的人称为 "特性对等之陷阱"。

虽然 "特性对等 "听起来像是一个合理的建议，但我们已经知道，人们大大低估了所需的努力，从而误判了这一选择和其他选择之间的关系。例如，即使只是定义 "现状（as-is）" 的范围，也会是一个巨大的努力，特别是对于已经成为业务核心的遗留系统。

随着时间的推移，大多数遗留系统已经 "膨胀"，因为新的特性被添加，而旧的没有被删除，导致许多特性都没有被用户使用（根据 2014 年 Standish 集团的报告，是 50%）。对过去 bug 和限制的妥协已经成为当前业务流程的 "必须 "要求，用户的工作方式由遗留的限制和其他东西决定。重建这些特性不仅是一种浪费，而且还代表着错过了建立现状真正需要的东西的机会。这些系统通常是在10年或20年前在前几代技术的限制下定义的，"原封不动" 地复制它们是非常没有意义的。

如果真的的确要求特性对等，那么这个模式也描述了可能需要做好的事情。这不是一条容易的路，也不是一条可以随便走的路。

## 怎么做 How It Works

特性对等是一个简单的概念。在一个更合适的技术栈中建立一个新的系统，其特性和行为与现有系统完全相同。每当有人问起新系统应该做什么时，我们就用 "做现有系统做的事" 来回答这个问题。要想确认其对等性，我们需要充分理解当前系统的作用，并能够验证新系统做了同样的事情。

### 范围是怎样的 -- 旧系统做了什么？

特征对等的第一部分是创建一个当前系统做了些什么的说明书。可能需要结合以下内容：

#### 系统调研

##### 用户操作

用户的角色是什么，他们可以看到系统中的哪些特性（菜单项），他们可以执行哪些操作。对于每个菜单项/操作项 -- 所涉及的页面是什么，数据项是什么，你能看到的校验逻辑是什么。对于采取该操作的用户，能观察到何种结果？

##### 批处理

系统中定义了哪些批处理作业？它们何时被触发，执行什么处理，能观察到何种结果？

##### 接口和集成

哪些系统被集成了？

- 这个系统向其客户提供了什么接口，有哪些契约（API、CFR、行为预期/副作用）？
- 这个系统消费了哪些接口，契约是什么？
- 注意那些通过数据库集成的系统或系统的一部分（见如下 “报表 / 数据” 和 “考古”）。

##### 核心算法

众所周知的业务规则和需要复制的计算--由用户操作、批处理触发。

##### 报表 / 数据

该系统创建什么报表，以什么格式，从什么数据，在什么时候，有多频繁？

在数据库中的数据是如何变化的？是否存在改变数据的触发器，是什么触发了这些触发器，以及触发了什么存储过程？这个兔子洞（rabbit hole）有多深？

哪些其他系统可以访问或正在使用这些数据？它们以什么方式改变数据，能观察到何种结果？

#### 考古

要完全理解一个系统的作用，往往需要考古。正是通过一个 "考古过程"，你了解到在批处理任务 “N” 运行后，改变页面 “A” 上的字段 “Y” 会导致数值 “Z” 出现在报表 “C” 上。执行这种考古工作可能需要投入大量的时间，以及那些对遗留系统最有经验的人的脑力。

#### Instrumentation - based on data, what is used?

It is well worth spending some time analysing existing reports such as access or other system logs to understand how the current system is used. If these are not available, then some investment in instrumenting the existing system may provide a good return as it could allow you to avoid unnecessary work based on data.

#### Feature value - can we drop features that are low value?

Whilst we are trying to avoid burdening the business when adopting this pattern, talking to users to understand what features are low value or not used can be helpful to manage your scope. This will re-introduce the organisational impacts/change that we were trying to avoid though.

### Use tests to ensure feature parity - the new system does what the old one did

Knowing what the old system does is the first challenge, but we also need to be sure that the new system does indeed work the same way. To verify this with confidence we need tests in place to prove that the new system does indeed have feature parity. Following is a list of areas where testing will need to be implemented.

#### User journeys and user experience

Use acceptance tests - to check that features you have built behave as expected from a user's perspective. Take care not to be reliant on these as the main validation approach as they are high up the testing pyramid. Additionally be mindful that decomposing a system into user functions, screens and flows could easily lead to too many of these types of tests.

Testing UI behaviour (including client side validation) is relatively simple to do: click here, expect to see some state, enter data there, click there, expect to see some different state. Use the appropriate tooling for your chosen tech - unit-like test frameworks for SPA application, or something like Cypress for a more traditional server side rendered application.

Where it is important (in an "expert user optimised" UI for example) the testing of layout (and look/feel) is harder but there are tools that can help (Galen with Selenium for example).

For the usability and layout you will likely be reliant on exploratory testing.

> Side Note: The [Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html) is a metaphor that tells us to group software tests into buckets of different granularity. It also gives an idea of how many tests we should have in each of these groups. Although the concept of the Test Pyramid has been around for a while, teams still struggle to put it into practice properly, and its guidance applies here too, where there are a number of potential traps that could tempt you towards a different shape.

#### Business logic - core algorithms

For core algorithms and core business logic, ensure that you have built a suite of unit tests around these parts of the existing system. These will provide executable specifications of the business logic / algorithm that is known to successfully run on the existing system. A modified TDD approach that includes porting these tests into the new tech stack can then be used giving high confidence in the new implementation. There is a risk associated with knowing that you ported the tests correctly - mutation testing could provide some additional reassurances - similar mutations cause similar failures across the old and new implementations.

#### Interfaces - as a service provider and as a service customer

As service provider: Create a test suite to execute against the existing system being replaced - an executable contract. Execute these same tests against the new replacement system checking for adherence to the contract. Beware the trap of the scope of these tests becoming too large, like in the case of the UI.

As customer: Use test mocks to validate that you are interacting with the provided services in the expected way. Like the case for core business logic, migrate these mocks to the new tech stack to ensure the new implementation continues to interact with provided system in the same way. Additionally use stubs of the external systems to provide known data sets for your new tests.

In both cases: Proxies can be a useful tool to use to ensure feature/interaction parity. By injecting a proxy into the communication path the interactions with the old system can be recorded. You can use these recordings to:

- Replay messages from customers - and check the new system's response
- Create stubs that can replay known good responses

Databases and reports: This can be really hard - like UI tests beware the top of the pyramid. Here the database / reports are another type of interface. Successfully testing them will require lots of test data - typically hard to create/manage.

### Implementation and tracking progress

With a completed system survey to define scope, and a comprehensive suite of tests in place to provide an executable specification of behaviour your implementation can proceed with relative confidence. But how to track progress?

#### By Menu Item / User Action

(or other parts identified when surveying system) This can be dangerous, as there is a risk that the things you are tracking may be sliced by layer (or architectural concern) Slicing by architectural layer severely impacts the delivery of value, and reduces visibility of progress. (i.e. no value is unlocked and no _real_ progress is made until a feature is delivered top to bottom).

A key assumption here is that the Item or Action can deliver Value for end users on it's own. Unfortunately this is often not the case, we might deliver 80% of individual process steps but still be unable to complete the whole business process within the new system. This is an unfortunately common situation, with high levels of completeness being reported and yet an inability to test or release usable software.

#### Vertical slices

Provide better transparency of progress, but it is likely that vertical slices will not be the output of the system surveys. So mapping has to be done - more work, and a risk that requirements fall through the gaps.

#### End to end processes

Track progress based on the full of migration of individual business processes into the new solution, so the test becomes: can a process be fully completed wholly within the replacement system. This can be combined with tracking by User Actions, above, but only if we ensure the work done to deliver individual process steps is prioritised by the "owning" business process.

The authors' preference would be to ensure that the definition of done for any piece of work includes (where possible) the complete end-to-end scope across all layers.

## When to Use It

We have presented above our view on what is required in order to apply this pattern well, and increase your chances of success. If feature parity is the aim, then there is significant work involved in determining what is required in terms of features, and more work associated with ensuring that the feature parity goal has been met through testing.

In general this is a pattern that we don't recommend. In fact Thoughtworks went as far as placing this very pattern on hold in our Technology Radar. We see this pattern as a huge missed opportunity. Often the old systems have bloated over time, with many features unused by users (50% according to a 2014 Standish Group report) and business processes that have evolved over time. Replacing these features is a waste. Instead, try to muster the energy to take a step back and understand what users currently need, and prioritize these needs against business outcomes and metrics.

We have however seen a couple of cases where this pattern is particularly applicable.

- **Highly optimised user interfaces for power users** are good candidates for recreating like-for-like. Consider agents using short-cut keys to execute trades at high speeds. To be effective at their job they may require hyper-optimised user interfaces that enable them to operate using only the keyboard. It may take a significant amount of time to become proficient, and changes that result in lower effectiveness cannot be tolerated.
- **Behaviour based on well known specifications:** Another example use case for applying the pattern could be systems that support engineering or scientific modelling. In the case of a finite element analysis solver for example, a given input should produce a given output - the laws of physics are not changing as part of the modernization project.

One could argue that in both of these cases the need for feature parity is somewhat localised - by which I mean constrained to a particular part of the system. It is questionable that the approach to managing scope of the modernization of the whole system be constrained by these localised use cases.

But these cases, while valid, are still exceptional. Overwhelmingly we've seen feature-parity exercises be a tale of frustration. The cost and effort required to properly understand existing features mushrooms, leading to corners being cut, and although some of these are unused features that ought to be cut, usually some vital features also feel the knife. If all goes well, the business pays a handsome sum of money for no improvement in support for the business. This is not a good story when enterprises know that their future depends on better technology engagement.

### Alternative approaches

- [Extract Product Lines](https://martinfowler.com/articles/patterns-legacy-displacement/extract-product-lines.html) or Extract Value Streams are both patterns that give strategies for identifying thin slices through an existing system. One of the key differences is that they both offer ways to shorten the feedback cycle while allowing elements of legacy to be disabled and turned off much sooner.
- Looking at the [business value](https://martinfowler.com/bliki/value-architectural-attribute.html) and making sure that is represented in any architectural decisions can often highlight issues with Feature Parity driven approaches.
- More "holistic" approaches can help highlight issues with Feature Parity by treating technology and business process as part of the same problem. Specifically in the case of Feature Parity this can often highlight that current business processes are a consequence of the workarounds and compromises required by the legacy technology. Just replacing the tech will leave at least half the problem unsolved.
- User research can help highlight that existing business processes are no longer fit for purpose. In one case by just having a few days of shadowing of existing staff it became clear feature parity was unsuitable as current processes were very broken, it's always a good thing to talk to the end users.

## Example: replacement of logistics systems

A logistics organisation was engaged with a plan to replace aging software used for the acceptance, route planning and delivery of packages. As part of this they agreed an IT led initiative with relatively low engagement with the business stakeholders. The technology view was that they could just do "feature parity" thus solving their pressing need to replace out of date tech. As part of this programme a major piece of work was done to replace the system that accepted the packages from clients. We become involved towards the end of this part of the programme.

We felt the low engagement with the business stakeholders presented a risk to the programme, especially as we were hearing via a different project that the business were feel frustrated with the development efforts. As part of this we spoke to the key stakeholders including the finance team. This is when we became aware of a review that had been done that indicated only a relatively small percentage of customers were profitable for the organisation. In turn this meant only a small subset of 'package types' were profitable, many lost the organisation money due to special handling requirements. So the business had a plan in place to stop handling those packages.

It turned out that a very large amount of effort in the "feature parity" project had been spent to deal with exactly those packages, the very ones the business said they no longer needed. The business had hoped that the processes and hence software would be much simpler without these difficult edge cases. In this case "feature parity" led to a large amount of time and money being spent handling requirements the business no longer had, while further damaging the reputation of IT in the eyes of the business.

## Example: e-commerce organisation re-platform

This organisation had enjoyed a period of rapid growth but had not prioritized IT spend for several years, this created a relatively urgent need to replace many elements of the current solution. For example during certain periods they had to slow down the number of sales to avoid overwhelming core systems, hardly ideal from a business point of view.

Many of the key business operations were handled by the same mainframe, which had been initially commissioned during the very earliest days of their e-commerce operations. Extracting elements from this system was clearly going to be technically challenging. At the same time business leaders having seen several disruptive failed projects wanted to minimize any further disruption to their staff. A further challenge was current processes and systems made it extremely difficult to prioritize product lines to migrate if a more incremental approach was used. In short it was very difficult to understand which things they sold made money and which things didn't so it was felt the only option was to move everything all at once. Based on these challenges it was felt just replicating what they had was the best and lowest risk approach.

Given current business processes were, on the face of it, all implemented together in the same mainframe this meant the scope of any "feature parity" replacement was in essence all the main activities and processes of the entire business. They embarked on an effort to document the "in scope" as-is processes for the replacement system, with the plan that this was going to be used as input for a vendor selection process.

Several things soon became clear. One was that it really was going to have to be almost every single activity that the business did, each effort to document "as-is" functionality uncovered more things that would need to included to give "feature parity". Secondly due to historical workarounds, years of shifting requirements alongside many outstanding bugs the last thing the actual people who worked with the legacy system wanted was the same thing. It almost always made their jobs harder and was a key source of error and delay. Finally due to the bugs and workarounds it became clear several key processes were in fact being run "off system" on hand-built spreadsheets. Key business data was extracted from the mainframe, used to run a business process via the spreadsheet, then later on the now modified data was uploaded to back the mainframe.

At this point it was felt by some that "feature parity" was becoming too risky, with scope continually growing. This was before the requirements gathering process was complete, and there was no clear end date for that effort. Our involvement ceased at this stage as we felt continuing with "feature parity" was too risky and would not deliver what the business needed, not least since lack of key business metrics made prioritisation of any more incremental approach impossible. Several years later they were still doing the "as is" requirements process, way past the original deadlines.

Lack of the right metrics and ability to prioritize elements of functionality from a business point of view can often force organisations into a "feature parity" approach. In this case we think it likely a initial effort to gather key business metrics around various product lines could have suggested ways to break the problem up. It's a great illustration that you can't make the right technical decisions without the right business context and involvement.

## Example: a successful replacement of a financial calculation service

One of our teams was working for a large financial organisation. They wanted to modernise an existing service that performed complex financial calculations. The specification of the financial calculations was fixed, the interface to the service was similar only the technology needed to be updated - from a J2EE Session EJB implementation, to a SOAP Web Service in the latest (at that time) version of Java.

The team created a rich test suite around the existing implementation with clean separation between set-up, execution and assertion responsibilities.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/tests_for_feature_parity.png)

Figure 1: tests_for_feature_parity

Once the tests were in place, the execution adapter could be replaced with minimal risk, and a new implementation created meeting the same executable specification as the old system.
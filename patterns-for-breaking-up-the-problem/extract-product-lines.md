# Extract Product Lines

> 本文是对 [Ian Cartwright](https://www.linkedin.com/in/ian-cartwright-282952/)， [Rob Horn](https://www.linkedin.com/in/rob-horn)，和 [James Lewis](https://bovon.org/) 的文章 [*Extract Product Lines*](https://martinfowler.com/articles/patterns-legacy-displacement/extract-product-lines.html) 的中文翻译。
>

*Identify and separate systems by product line.*

---

Many applications are built to serve multiple logical products from the same physical system. Often this is driven by a desire for reuse. "Hmm, consumer loans look quite similar to business loans" or "clothes are a product, so are made-to-measure curtains, how different can they be?". A major problem we come across is that superficially the products look similar but they very different when it comes to the detail.

Over time, single systems serving multiple products can become over generic, with code evolving to handle all possible combinations of all possible products. For example, for a generic system that is designed to handle n products with n changes per product the amount of testing that must be done to check all possible combinations is n factorial. That's a number that gets big quickly. It also explains why many of the applications of this type that the authors have encountered have had very little in the way of automated test coverage, instead relying on huge, often manual regression suites. It's simply not possible to test so many different codepaths.

> **The one with conditionals**
>
> In another example from the retail banking domain, static analysis led us to a Java class with 61 nested if statements. This led Daniel Terhorst-North to make the observation that there is no correct number of nested if statements since if you have one if statement, it's clearly OK to add another, but if you have 60 of them, what else are you going to do? Admitedly this example is a pathological one but it does illustrate how single codebases serving multiple products - loans in this case - can evolve unless we are careful.

The problem is often therefore one of economics. It is difficult to accept that developing and maintaining a system per product might make better economic sense than developing and maintaining a single system. By splitting by product we are taking advantage of the fact that changes to multiple products can be made simultaneuously, and reducing risk by avoiding the combinatorial explosion of changes that may introduce defects in unwanted places.

Of course this is a trade-off - we don't want separate applications for red trousers versus black trousers, but we may want one for off-the-peg versus made-to-measure, or Home insurance vs Pet insurance.

It is also common for different product lines to have very differerent value streams as described in [Extract Value Streams](https://martinfowler.com/articles/patterns-legacy-displacement/extract-value-streams.html).

## How It Works

**Identify the product or product lines within the system**. This will form the thin slice to be built / migrated. Look to identify all the capabilities that the existing system provides and map them to the new product. We tend to look through different lenses when identifying capabilites, data, process, users etc.

**Identify shared capabilities**. Identify if the different product lines have BusinessCapabilities that are shared. There are several ways of approaching this, which we cover in UnderstandingYourBusinessCapabilities. Our advise as ever is to value Use over Reuse, so if in doubt try and limit the number of shared capabilites as much as possible

**Choose who goes first**. Which product lines are you going to move off first? One approach we like is to think in terms of risk. After understanding the risk to the business of migration, we often like to take *the second riskiest product line*. This may feel counter-intuative and that we should take the least risky option first but actually we like to identify a product that is meaty enough to keep the business' attention and cause them to prioritise funding, but not so risky that the business will fail if there are problems.

**Identify your target software architecture**. It is very rare that we advise big bang replacements, which in this case would mean building out all the software for all of the products at once. Instead, look to identify the appropriate architecture for the thin slice identified in step 1.

**Identify your technical migration strategy**. As we discuss in the section on technology migration patterns, there are a number of different options that can be deployed depending on current constraints. If it's a simple web-application then it may be possible to use ForkByUrl. In other cases where ForkingOnIngress is appropriate it may be better to choose the MessageRouter pattern. Keep in mind that a TransitionalArchitecture may be necessary.

## When to Use It

You have a system with easily identifiable product lines that would benefit from:

1. Being worked on independently. Breaking up the system into separate products means that teams can be formed around the individual products allowing for progress to be made without the typical problems of change co-ordination including merge hell and long regression cycles.
2. That have different non-functional characteristics. You want to be able to offer different SLO's for each product. For example different load requirements for a given latency.
3. Different rates of change. Some product lines are stable while other products are undergoing active development. Breaking up the system means that you do not risk changes to the stable products.

## Insurance Example

In the insurance domain, different product types have very different characteristics. For example Vehicle insurance is typically high volume but low margin, whereas Home insurance is the opposite. It is also highly competative so the ability to make changes quickly is very important. One insurance company we worked with had developed a 3-tier architecture that served as the quoting engine for all the different products the insurer offered including Vehicle, Life, Home and Pet lines as shown in figure 1.

![img](https://martinfowler.com/articles/patterns-legacy-displacement/insurance-app-arch.png)



> **The trouble with Tiers**
>
> For many years it was most common to think in terms of the systems architecture first and the business or product architecture second. This has led to both a preponderonce of n-layer systems in the enterprise and also to many aging COTS products. Many systems build this way suffer from long lead-times for all changes as they must be coordinated across the n-layers.
>
> In n-layer systems that serve multiple products, it's generally impossible to scale the system differently for each product line. It is very unusual for multiple products to have exactly the same non-functional characteristics. This isn't to say that layering is bad in general, in fact it's a [Good Thing for organising codebases](https://martinfowler.com/bliki/PresentationDomainDataLayering.html).

## Understand the outcomes you want to achieve

The product owners for the business were increasingly frustrated at the lead time to change which was long and getting longer. They decided to get Thoughtworks in to take a look at their architecture and development processes to see if we could identify why things were so bad. The value stream map for the development process identified a number of constraints that were contributing to lead times exploding. While each technical domain was decoupled from the others, the different business domains were tightly coupled together. This meant adding a new requirement for the Vehicle product would often impact Home, Life and so on. These changes equired both careful thought and extensive regression testing before the usually fraught deployments. The multi-product architecture also imposed a limit on the number of people able to work on the codebase safely, further slowing progress.

Finally, due to the volume requirements of the Vehicle product line and the growth of the business, the underlying data store had to be scaled often, requiring downtime to all the other products hosted by the system.

## Decide how to break the problem up into smaller parts

As a result, the insurer decided to migrate away from their n-tier architecture organised around technical capabilities towards one organised by product lines. The products were already identified: Vehicle, Home, Life and Pet insurance. Once these were understood the different capabilities that they each needed were identified, for example the individual question sets, quoting and customer account as well as more technical capabilities such as Authentication and Authorisation. Customer Account was identified as a key shared capabilty that all of the product lines would use, a good example of adopting the Coordination approach from the EA Magic Quadrant which is described in more detail in [The One True Ring](https://martinfowler.com/articles/patterns-legacy-displacement/one-true-ring.html).

The next thing that needed to happen was to identify where to start. In order of revenue to the company, the product lines rated as Vehicle, Home, Life and Pet. Whilst in terms of customer numbers the order was reversed. As a result it was decided that Home insurance would be the first product line implemented separately. This balanced risk to the business of something going wrong with enough revenue to make the choice important.

## Successfully deliver the parts

![img](https://martinfowler.com/articles/patterns-legacy-displacement/insurance-event-driven.png)



> There is a lot going on in this picture, but in essence it's showing a very high level view of the interactions between a subset of the different capabilities needed to sell insurance to a customer.
>
> 1. Once a customer has filled in the myriad questions required for insurers to quote and submitted their form (known as a Risk in the field), the Home Insurance application publishes a RequestQuote command.
> 2. The Quoting Engine, subscribed to a RabbitMQ topic, receices the RequestQuote command and does it's stuff (behind the scenes issues a number of internal and external requests to partners, brokers etc. In the case of this insurer, it involved sending requests of various types and using various protocols to over 40 other insurers. Honestly, if there was a standard for this, the whole thing would be a whole lot easier!
> 3. As replies from the downstream systems are received, QuoteReceived events are pulished onto another RabbitMQ topic by the Quoting Engine.
> 4. A number of things happen next since a number of capabilities in addition to the Home Insurance system are interested in the fact that a quote has been received. These include the Account Management capability and the Enterprise Data Warehouse (EDW). Fortunately these capabilities are also subscribed to the right Topic and can pick up these events as they need them.
> 5. A customer may then decide to proceed with one of the quotes to actually purchase a poicy and clickthough to do so. At this point a PolicyPurchased event is published and the Home Insurance system pats itself on the back in the knowledge of a job well done.
> 6. Finally, any other capabilities interested in the PolicyPurchased event receive it via RabbitMQ again and do their own thing (store it for reporting purposes in the case of the EDW and so that the Customer can easily retrieve it later in the case of Account Management.
>
> One nice aspect of using published business events in this way was the ease with which dependent systems such as as the data warehouse could be updated. In this case, the team created a simple adaptor over the EDW that turned the stuff thats happening in the new world into the star schema required by the old.

The above figure shows the event-driven architecture that the team started to build out. Communcation with the Quoting Engine and with the Customer Management capabilities was via events passed over a RabbitMQ message bus. These events were also propagated to the existing enterprise data warehouse for reporting purposes.

As the team built out the new systems to the side of the existing one, preparations were put in place to cut over traffic from the legacy system to the new one. One downside to moving the product line in it's entirety was that the question set had to be implemented in full before customers could be switched over. Due to this contraint the new system went into a Beta phase where certain customers were offered the option to opt-in to use the beta version. Those that opted in also had the opportunity to provide feedback on the new look and feel. As the new system was progressively enhanced and the final cosmetic features were added, a go-nogo decision was taken and customers were gradually redirected to the new system over the course of several weeks. First one percent, then five, then ten percent etc. This allowed the team and business to grow in confidence that the new system was performing as expected, both from a functional and non-functional point of view. Finally, the new system was serving one hundred percent of traffic for Home Insurance. The team then turned to ongoing product development.

## Change the organization to allow this to happen on an ongoing basis

After the success of the first migration, attention turned to the next challenge, moving Vehicle insurance using the same approach and the pattern was repeated until the migration was complete and the old system turned off.

Meanwhile, and gradually, the whole technical organisation moved away from a project-based approach to development to a product-centric one. This of course had teething problems. Product Ownership is a skill that needs to be built over time and so the transition was a gradual one. They also adopted the same approach to traditional IT Operations, and under the guidance of the CTO and Chief Architect moved towards a platform product engineering approach for on-demand infrastructure and then data and analytics.

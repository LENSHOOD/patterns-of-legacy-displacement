# Legacy Mimic

>  本文是对 [Ian Cartwright](https://www.linkedin.com/in/ian-cartwright-282952/)， [Rob Horn](https://www.linkedin.com/in/rob-horn)，和 [James Lewis](https://bovon.org/) 的文章 [*Legacy Mimic*](https://martinfowler.com/articles/patterns-legacy-displacement/legacy-mimic.html) 的中文翻译。

*New system interacts with legacy system in such a way that the old system is not aware of any changes.*

---

When *incrementally* replacing legacy systems with new ones, it will not be possible to cleanly isolate the new world from the old. A [Transitional Architecture](https://martinfowler.com/articles/patterns-legacy-displacement/transitional-architecture.html) requires that the new world provides data (or some other interaction) in order to "keep the lights on".

When this is the case the new system must meet some existing (often implicit) contract, and so the new system must become a Legacy Mimic.

## How It Works

Through exploring the legacy system's technical architecture and target architecture vision, and understanding the existing and to-be business processes, seams can be discovered and exploited allowing the problem to be broken up into parts.

The Legacy Mimic pattern is an enabler for these seams, and creates options for, and is an implication of, different sequencing approaches.

A Legacy Mimic often realises the [Anti-Corruption Layer](https://martinfowler.com/bliki/DomainDrivenDesign.html) pattern from Eric Evans's Domain Driven Design book. There are similar forces at play as the [Anti-Corruption Layer](https://martinfowler.com/bliki/DomainDrivenDesign.html)'s intent is to:

> Create an isolating layer to provide clients with functionality in terms of their own domain model. The layer talks to the other system through its existing interface, requiring little or no modification to the other system. Internally the layer translates in both directions as necessary between the two models.

Similar to an [Anti-Corruption Layer](https://martinfowler.com/bliki/DomainDrivenDesign.html) a Legacy Mimic's implementation will typically use Services, Adapters, Translators and Facades.

There are at least 2 types of mimics that we commonly see, and are most easily explained in terms of providing or consuming services.

A **Service Providing Mimic** will encapsulate a new implementation behind a legacy interface. Legacy components will be able to interact with it using the legacy interface and not know that they are collaborating with that new implementation.

A **Service Consuming Mimic** will collaborate with the legacy systems that have not yet been replaced using their existing legacy interfaces. Again this interaction will be transparent to the old system.

## When to Use It

To further illustrate these different types of mimics this figure shows a monolythic legacy system that supports 3 business processes - Sales, Logistics and Business Performance.

![Legacy Mimic - Before example](https://martinfowler.com/articles/patterns-legacy-displacement/mimic-example1.png)



An option being considered is to use the [Extract Value Streams](https://martinfowler.com/articles/patterns-legacy-displacement/extract-value-streams.html) for the Logistics capability. Doing so could result in a transitional architecture like:

![Legacy Mimic - Example with mimic, acl and event interception](https://martinfowler.com/articles/patterns-legacy-displacement/mimic-example2.png)



In order for the new logistics system to process the fulfillment of sales, [Event Interception](https://martinfowler.com/articles/patterns-legacy-displacement/event-interception.html) is proposed. In this example the Event Interceptor is an example of a Service Providing Mimic - it is conforming to the legacy interface (consumption of legacy events).

In order for the Business Performance process to continue to function, again the use of the Legacy Mimic pattern is proposed, but this time as a Service Consuming Mimic. It will replicate the required logistics metrics into the legacy reporting database (conforming to the legacy database's schema and semantics).

Both of these components will not endure in within the target architecture of the system - they are transitional.

The existing Logistics Partner System must still be integrated with, so an Anti-Corruption Layer is used by the new Logistics system. As this will endure it is not considered a mimic (or transitional), but the creation of the [Anti-Corruption Layer](https://martinfowler.com/bliki/DomainDrivenDesign.html) is used in order that the domain model of the new Logistics System is not compromised by the model used by that external system.

![Legacy Mimic - Sequence diagram with mimics and ACL](https://martinfowler.com/articles/patterns-legacy-displacement/mimic-example3.png)
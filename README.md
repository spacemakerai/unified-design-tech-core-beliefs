<!-- omit in toc -->
# Tech core beliefs, our guiding principles for time to value; speed, quality, reliability and agility

This document was created on the belief that instead of making strict rules on how we build products and services in Unified Design @ Autodesk, we believe in alignment on how, by creating some guiding principles. We strongly believe that time to value is best served by autonomy and thereby letting our people choose the right tool for the job every time. In that we also say that we choose a little chaos over control and big platform architectures. 

Why is autonomy important, and what is it, really? It’s not about autonomy for autonomy’s sake, or because it makes people in the squad happy - it’s about **time to value.**

This document dives into what this means for infrastructure, architecture and way of working.

- [Building an Architecture for time to value](#building-an-architecture-for-time-to-value)
- [There is no perfect answer - The Art of Compromise](#there-is-no-perfect-answer---the-art-of-compromise)
- [Prefer sharing knowledge _over_ sharing code](#prefer-sharing-knowledge-over-sharing-code)
- [Minimize ownership, make it somebody else’s problem](#minimize-ownership-make-it-somebody-elses-problem)
- [Avoid “leaky” abstractions](#avoid-leaky-abstractions)
- [You ain’t gonna need it](#you-aint-gonna-need-it)
- [Choose simple over easy](#choose-simple-over-easy)
- [Moving fast without breaking things](#moving-fast-without-breaking-things)
- [It should be as safe as possible to fail](#it-should-be-as-safe-as-possible-to-fail)
- [Embrace an open source mindset, regardless of ownership](#embrace-an-open-source-mindset-regardless-of-ownership)
- [What was the preferred choice yesterday, might not be the preferred today](#what-was-the-preferred-choice-yesterday-might-not-be-the-preferred-today)
- [Internal services, not shared services](#internal-services-not-shared-services)
- [Learn to let go of control and embrace chaos](#learn-to-let-go-of-control-and-embrace-chaos)


## Building an Architecture for time to value
_Ultimately we want to optimize for time to value, and we believe the biggest driver is a team's ability to be fully autonomous. Instead of the traditional approach of focusing on consistency and reuse, an autonomous team will tend to create decoupled, messy, duplicated and emergent architectures._
___

It is a common reflex for software developers to strive for consistency and reuse, hoping you can achieve some sort of control of the total system and to make sure you don’t waste resources by building the same things twice. In the long run though, these architectures often end up as very hard to change.

The more a component is reused the more it will need to grow into a one-size-fits all solution which will both be a huge endeavour to create and maintain, but may also stagger innovation as changing “the service” will have huge consequences. Teams might even hesitate to try a certain idea because they know it will be too hard to get everybody to agree and adapt to the change they need to perform in shared services.

We believe the individual teams ability to innovate, try new technology, change services, delete old things, far outweighs the organizational need for overarching control of the architecture.

## There is no perfect answer - The Art of Compromise
_We should never accept it when a technical solution is presented as “perfect” or “with no downside”. Any solution involves compromises - and we must always strive to be aware of them, and enlighten others about them when making choices. The phrase “best practice” is something we should avoid thinking of as a silver bullet, instead, it can be a possible “better practice”._
___

Software engineering is largely the art of balancing competing trade-offs. It's impossible to optimize every factor (features, speed, ease of use, ease of training, ease of future software change, time spent, etc.). We approach this as the quest to satisfy the varying needs of the customer/user using limited resources and time. Not only must all these options be weighed and considered, but in order to keep making informed choices over time, the whole team needs to be kept knowledgeable about the various trade-offs. Thus, being able to communicate tradeoffs is also crucial. We choose to do X because it's better than the alternatives for our company and because we can describe why it's better in a practical sense, not out of habit, or because someone told us to do so.

So, how do we in Unified Design balance the trade-offs? In short, we optimize for time to value, with an emphasis on autonomy. Furthermore, we have set out a set of principles and guidelines to maintain the minimum viable level of alignment.

## Prefer sharing knowledge _over_ sharing code
_When you are solving the same problem as another squad, it can be tempting to share code (or infrastructure). Sharing code lets you share the burden of maintenance and makes it easier to learn from each other through the code. However, when what was initially a shared problem evolves, you start paying the cost. Whenever your squad wants to do an adjustment, you have to keep the other stakeholders in mind. This will increase your squad’s time to value and increase the complexity of the code._
___

Learning from another squad and duplicating the code gives many of the same benefits, but at a lower risk. This approach requires a strong organizational sharing culture. This does not mean that sharing code is bad every time. There are cases where it's beneficial. But often more in tooling and libs than application or customer facing problem solving which quickly evolve in different directions. 

## Minimize ownership, make it somebody else’s problem
_Inventing and caring about a thing has a huge hidden cost - you have to build/configure it, maintain it, it spreads focus, broadens competence needs, takes time in maintenance, increases the potential for technical debt, etc. All in all, it dramatically increases time to value (in a non-transparent way). It also increases time spent on non-critical tasks. Since our most expensive resource is hours spent by our employees, most hours should be spent building the unique stuff only we have - our business logic and user interaction._
___

So, suppose an existing, well-designed, maintained software lib or managed service can do 70% of the job or fits only 70% unless you redefine your problem. In that case, you should work hard to reduce your problem to fit into this slot before you even consider doing it yourself. AWS Lambdas are an excellent example of something that has significant constraints on runtime, environment, and resources but should almost always be favored for its ease of use and reliability. Another example would be product support. If that is not your main product, why would you ever try to build a support platform yourself when you have SaaS like Intercom? You shouldn't; these SaaS tools’ main mission is to solve support the best way possible, pouring millions into that goal. Let somebody else build it, and focus instead on building what is strategic to your product.

And if you ever consider building it, be aware that what we build WILL have lower Quality of Service than what you can get “off the shelf” - others have spent thousands of hours iterating over the existing service. The more our business grows and the more users we have that rely on our services every day, our QoS requirements will grow. Be humble and thankful to the sacrifices of all those engineers solving problems that we have as parts of our total product delivery.  

So, start every day looking for stuff we could throw away and replace with something somebody else owns for us - and accept owning more stuff only when you are desperate

Own as little as possible, and build the things you own so that others don’t need to know or care how it works. This reduces time to value dramatically, as you can move forward independently of both the services you consume and of your consumers.

## Avoid “leaky” abstractions

_Avoid “leaky” abstractions between domains and ownerships - your domain specific requirements should not be encoded in a service or component owned by others. Squads should strive not to have dependencies on each other, making them subject to the prioritizations in other squads. As we are working on the same product, with shared data, there will always be a need for squads to interact. These interactions should be as static and well defined as possible._
___

_Example_: if a service that stores geometry needs to adhere to a certain structure to work with your service, but this requirement is true only for your service - not for other users of the service. This would limit the ones that own the service from innovating at their own pace - OR it would cause your service to often crash unexpectedly. The abstraction is “leaky”. 

_Example_: Sharing infrastructure services between two squads, where the infrastructure serves the need of one squad well, while the other needs to do many changes. If the first squad needs to “follow” the other and do extra work to keep using the service, the abstraction of the infrastructure service is “leaky”.

_Example_: if one squad provides a service to other squads (clients), where the clients are happy with the QoS, but the squad wants to do changes, eg. to ease maintenance or increase reliability. If other squads need to do changes in order to consume the changed service, the abstraction is “leaky”.

Every hour spent on such things is an hour wasted, and every change not made because of the dependency to others is an opportunity lost.

The stuff you need to care about (how it works), you need to own all of that! Everything else should be somebody else’s problem. 

## You ain’t gonna need it 
_We must never build or introduce things before they are strictly and expressly needed. As engineers we often have a desire to build tooling or generic services for many use cases that we think will be nice to have in the future, even though there is only one use case right now. This is never a good prioritization - that time should be spent on our more immediate concerns._
___

When introducing third party services or components, it can be tempting to pick alternatives with a lot of extra features “out of the box” in addition to the need you are looking to fulfill. Making a decision based on this leads down a dangerous path of “pulling in” a lot of stuff that was not needed for the specific use case, without considering the fit for our needs. Downsides include limiting our ability to choose the best solution for other capabilities we need at any given time, reducing observability, increasing complexity, and increasing the stuff you need to care about/own. 

This principle also holds for things that have been built in the past. Things that have been built in the past can be especially hard to get rid of because of the sunk cost fallacy. Thoughts like “we already have it” and “it doesn’t take that much work to maintain” can make it hard to set aside time to deprecate and remove things. But in the long run these dependencies can come with debt. 


## Choose simple over easy
_It’s often tempting to focus on ease of use (e.g. easy to install) over making truly isolated, observable and simple components (easy to understand). We believe a key enabler for keeping time to value low in the long run is reducing complexity in our software._
___

In designing software we should focus on pulling things apart, yielding isolated and observable components with minimal dependencies. This means they can be understood, changed, replaced or reused individually. Over time software tends to be more and more expensive to change, and when we need to extend or fix parts of it our time spent is often dominated by our ability to understand the program. Although having processes, type systems, tests etc will help, they can still only take you so far if you don’t have a truly simple system.

In control theory, observability is a measure of how well internal states of a system can be inferred from knowledge of its external outputs. In Software Engineering, this translates to having a clear and transparent relationship between what you put into a service or functions, what comes back and any side effects. With low observability comes inevitable complexity, mistakes and high cost of change.  We must always strive to maximize the observability of our components and services.

## Moving fast without breaking things
_Minimizing time to value is about speed, but it is also about reliability. Value can not be received by the user unless the product is highly reliable - the things you build must not break in production, and even more important they must not break other things already in production - potentially reducing value that was already in the product._
___

So, you must always make sure to do the necessary QA on anything you release. If this QA takes time (challenging your ability to move fast) you must look for ways to bring down the time it takes for QA - without reducing your control over reliability. This could mean test automation, but it could just as well be about reducing complexity or improving abstractions to make it easier to analyze the impact of a change.

Time to value and reliability go hand in hand - done right, the efforts to maintain one will help the other. 

## It should be as safe as possible to fail
_Evidently, it is impossible to prevent failures or someone from breaking something at some point in time. A typical reaction to avoid failures is to add multiple layers of processes, but layers of processes will still be unable to prevent them from ever happening. One should move fast without breaking things but also embrace that something will fail. Consequently, we reduce the impact of mistakes by designing services with technical isolation_
___

By keeping isolation, fault tolerance, and resilience in mind, we focus on making explicit and deliberate choices about a service's internal and external dependencies to make it easy to reason about what the service depends on and to which degree, as well as to maintain a high degree of confidence about what change can trigger side effects. In addition, avoiding tight coupling and dependencies between other teams' services reduces the chances of cascading failures. 

## Embrace an open source mindset, regardless of ownership

_Even though ownership is clearly defined, we must always encourage and celebrate the input and help of others - whether it means another squad writing pull requests in your code or someone from the “outside” challenging your technical choices._

## What was the preferred choice yesterday, might not be the preferred today

_We make decisions based on assumptions and what we know all the time. But as tech evolve at a rapid pace, it's important to double check your truth when making an important decision._

___

What was missing from a cloud provider yesterday, might not be missing today. What we needed to develop ourselves yesterday might be possible to outsource and minimize ownership of today. What was the preferred way of doing something, might not be today. It is important to always try to look for solutions outside our daily ecosystem before we conclude on something.

## Internal services, not shared services
_We should always strive to have as few services as possible central to many parts of our architecture. As shared services introduce organizational complexity, we need to fit other teams needs and we can easily end up in a putting-triangles-in-square-holes situation._
___

Shared services have a tendency to grow over time, to fit more and more needs, since it might be useful, or it might be a good fit, or it is just easy to just put it there in the first place. And we should be really careful when introducing them, and not introduce hasty abstractions if it could just be duplicated. This does not mean however that all decisions to make shared services are wrong, in some cases we need to share data between services or teams. 

In these cases, we should however not introduce a shared service with shared ownership between teams. But rather state clear ownership to an internal service that happens to be used by multiple teams in the organization. The owner of the internal service should treat the other teams as customers. And the service as a product they deliver to them. This means setting a clear mission for the service, listen to the customers, but also give pushback if the customers want something that does not fit the mission. Have a clear message for what the service is for and what it is not. 

A few guiding principles when introducing an internal service:

- Keep it small and simple, solve one thing well
- Document the API (and keep it up to date)
- Validate- don't expect others to know the internals of your service

## Learn to let go of control and embrace chaos 
_As the company and the complexity of our software grows, we will inevitably feel that we lose more and more control. And our focus on autonomy will make this feeling of chaos come sooner than in a strictly ruled organization. But make no mistake, in any organization the sense of control will fade when the complexity gets big enough._
___

So how do we feel that we have control if we do not take it by building a platform, having testers or having rules or processes for every little thing. We have to trust that the autonomous squads have control in what they own. That the squads are able to zoom out of their own squad and thereby communicate and share findings that other squads should or could benefit from knowing. That we are able to align our squads by talking, setting higher level goals and visions. We need to trust that other people know what they are doing, and that they strive to do the best for both their squads and the company at the same time. It also means that people should speak up if they wonder, feel they have a suggestion others could benefit from, are curious about other things than the things they own in their own squad - because talking to each other about anything, builds trust over time. Control in Unified Design is about trusting each other and the choices everyone makes, and being curious instead of controlling if you wonder about something. 

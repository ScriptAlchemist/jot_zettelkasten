# Embracing Risk

> Written by Marc Alvidrez
> Edited by Kavita Guliani

You might expect Google to try to build 100% reliable services-ones that
never fail. It turns out that past a certain point, however, increasing
reliability is worse for a service (and its users) rather than better!
Extreme reliability comes at a cost: maximizing stability limits how fast
new features can be developed and how quickly products can be delivered
to users, and dramatically increases their cost, which in turn reduces
the numbers of features a team can afford to offer. Further, users
typically don't notice the difference between high reliability and
extreme reliability in a service, because the user experience is
dominated by less reliable components like the cellular network or the
device they are working with. Put simply, a user on a 99% reliable
smartphone cannot tell the difference between 99.99% and 99.999% service
reliability! With this in mind, rather than simply maximizing uptime,
Site Reliability Engineering seeks to balance the risk of unavailability
with the goals of rapid innovation and efficient service operations, so
that users' overall happiness-with features, service, and performance-is
optimized.

## Managing Risk

Unreliable systems can quickly erode users' confidence, so we want to
reduce the chance of system failure. However, experience shows that as
we build systems, cost does not increase linearly as reliability
increments-an incremental improvement in reliability may cost 100x more
than the previous increment. The costliness has two dimensions:

### The cost of redundant machine/compute resources

  The cost associated with redundant equipment that, for example, allows
  us to take systems offline for routine of unforeseen maintenance, or
  provides space for us to store parity code blocks that provide a
  minimum data durability guarantee.

###  The opportunity cost

  The cost borne by an organization when it allocates engineering
  resources to build systems or features that diminish risk instead of
  features that are directly visible to or usable to end users. These
  engineers no longer work on new features and products for end users.

IN SRE, we manage service reliability largely by managing risk. We
conceptualize risk as a continuum. We give equal importance to figuring
out how to engineer greater reliability into Google systems and
identifying the appropriate level of tolerance for the services we run.
Doing so allows us to perform a cost/benefit analysis to determine, for
example, where on the (nonlinear) risk continuum we should place Search,
Ads, Gmail, or Photos. Our goal is the explicitly align the risk taken
by a given service with the risk the business is willing the bear. We
strive to make a service reliable enough, but no **more** reliable than
it needs to be. That is, when we set an availability target of 99.99%,
we want to exceed it, but not by much: that would waste opportunities to
add features to the system, cleanup technical debt, or reduce its
operational costs. In a sense, we view the availability target as both a
minimum and a maximum. The key advantage of this framing is that it
unlocks explicit, thoughtful risk taking.

## Measuring Service Risk

As standard practice at Google, we are often best served by identifying
an objective metric to represent the property of a system we wan to
optimize. By setting a target, we can assess our current performance and
track improvements or degredations over time. For service risk, it is
not immediately clear how to reduce all of the potential factors into a
single metric. Service failures can have many potential effects,
including user dissatisfaction, harm, or loss of trust; direct or
indirect revenue loss; brand or reputational impact; and undesirable
press coverage. Clearly, some of these factors are very hard to measure.
To make this problem tractable and consistent across many types of
systems we run, we focus on **unplanned downtime**.

For most services, the most straightforward way of representing risk
tolerance is in terms of the acceptable level of unplanned downtime.
Unplanned downtime is captured by the desired level of **service
availability**, usually expressed in terms of the number of "nines" we
would like to provide: 99.9%, 99.99%, or 99.999% availability. Each
additional nine corresponds to an order of magnitude improvement toward
100% availability. For serving systems, this metric is traditionally
calculated based on the proportion of system uptime (see [Time-based
availability](https://sre.google/sre-book/embracing-risk/#risk-management_measuring-service-risk_time-availability-equation)).

## Time-based Availability

Using this formula over the period of a year, we can calculate the
acceptable number of minutes of downtime to reach a given number of
nines of availability. For example, a system with an availability target
of 99.99% can be down for up to 52.56 minutes in a year and stay within
its availability target; see **Availability Table** for a table.

At Google, however, a time-based metric for availability is usually not meaningful because we are looking across globally distributed services. Our approach to fault isolation makes it very likely that we are serving at least a subset of traffic for a given service somewhere in the world at any given time  (i.e., we are at least partially "up" at all times). Therefore, instead of using metrics around uptime, we define availability in terms of the `request success rate`. **Aggregate availability** shows how this yield-based metric is calculated over a rolling window (i.e., proportion of successful requests over a one-day window).

## Aggregate availability

For example, a system that serves 2.5M requests in a day with a daily
availability target of 99.99% can serve up to 250 errors and still hit
its target for that given day.

In a typical application, not all requests are equal: failing a new user
sign-up request is different from failing a request polling for new
email in the background. In many cases, however, availability calculated
as the request success rate over all request is a reasonable
approximation of unplanned downtime, as viewed from the end-user
perspective.

Quantifying unplanned downtime as a request success rate also makes this
availability metric more amenable for use in systems that do not
typically serve end users directly. Most non-serving systems (e.g.,
batch, pipeline, storage, and transactional systems) have a well-defined
notion of successful and unsuccessful units of work. Indeed, while the
systems discussed in this chapter are primarily consumer and
infrastructure serving systems, many of the same principles also apply
to non-serving systems with minimal modification.

For example, a batch process that extracts, transforms, and inserts the contents of one of our customer databases into a data warehouse to enable further analysis may be set to run periodically. Using a request success rate defined in terms of records successfully and unsuccessfully processed, we can calculate a useful availability metric despite the fact that the bath system does not run constantly.

Most often, we set quarterly availability targets for a service and track our performance against those targets on a weekly, or even daily, basis. This strategy lets us manage the service to a high-level availability objective by looking for, tracking down, and fixing meaningful deviations as they inevitably arise. See **Service Level Objectives** for more details.

## Risk Tolerance of Services

What does it mean to identify the risk tolerance of a service? In a formal environment or in the case of safety=critical systems, the risk tolerance of services is typically built directly into the basic product or service definition. At Google, services' risk tolerance tends to be less clearly defined.

To identify the risk tolerance of a service, SREs must work with the product owners to turn a set of business goals into explicit objectives to which we can engineer. In this case, the business goals we're concerned about have a direct impact on the performance and reliability of the service offered. In practice, this translation is easier said than done. while consumer services often have clear product owners, it is usually for infrastructure services (e.g., storage systems or a general-purpose HTTP caching layer) to have a similar structure of product ownership. We'll discuss the consumer and infrastructure cases in turn.

## Identifying the Risk Tolerance of Consumer Services

Our consumer services often have a product team that acts as the business owner for an application. For example, Search, Google Maps, and Google Docs each have their own product managers. These products managers are charged with understanding the users and the business and for shaping the product for success in the marketplace. When a product team exists, that team is usually the best resource to discuss the reliability requirements for a service. In the absence of a dedicated product team, the engineers building the system often play this role either knowingly or unknowingly.

There are many factors to consider when assessing the risk tolerance of services, such as the following:

* What level of availability is required?
* Do different types of failures have different effects on the service?
* How can we use the service cost to help locate a service on the risk continuum?
* What other service metrics are important to take into account?

## Target level of availability

The largest level of availability for a given Google service usually depends on the function it provides and how the service is positioned in the marketplace. The following list includes issues to consider:

* What level of service will the users expect?
* does this service tie directly to revenue (either our revenue, or our customer' revenue)?
* Is this a paid service, or is it free?
* If there are competitors in the marketplace, what level of service do those competitors provide?
* Is this service targeted at consumers, or at enterprises?

Consider the requirements of Google Apps for Work. The majority of its users are enterprise users, some large and some small. These enterprises depend on Google Apps for Work services (e.g., Gmail, Calendar, Drive, Docs) to provide tools that enable their employees to perform their daily work. Stated another way, an outage for a Google Apps for Work service is an outage not only for Google, but also for all the enterprises that critically depend on us. For a typical Google Apps for Work service, we might set an external quarterly availability target of 99.99%, and back this target with a stronger internal availability target and a contract that stipulates penalties if we fail to deliver to the external target.

YouTube provides a contrasting set of considerations. When Google acquired YouTube, we had to decide on the appropriate availability target of the website. In 2006, YouTube was focused on consumers and was in a very different phase of its business lifecycle then Google was at the time. While YouTube already had a great product, it was still changing and growing rapidly. We set a lower availability target for YouTube than for our enterprise products because rapid feature development was correspondingly more important.

## Types of failures

The expected shape of failures for a given service is another important
consideration. How resilient is our business to service downtime? Which
is worse for the service: a constant low rate of failures, or an
occasionally full-site outage? Both types of failures may result in the
same absolute number of error, but may have vastly different impacts on
the business.

An illustrative example of the difference between full and partial
outages naturally arises in systems that serve private information.
Consider a contact management application, and the difference between
intermittent failures that cause profile pictures to fail to render,
versus a failure case that results in a users's private contacts being
shown to another user. The first case is clearly a poor user experience,
and SREs would work to remediate the problem quickly. In the second
case, however, the risk of exposing private data could easily undermine
basic user trust in a significant way. As a result, taking down the
service entirely would be appropriate during the debugging and potential
clean-up phase for the second case.

At the other end of services offered by Google, it is sometimes
acceptable to have regular outages during maintenance windows. A number
of years ago, the Ads Frontend used to be one such service. It is used
by advertisers and website publishers to set up, configure, run, and
monitor their advertising campaigns. Because most of this work takes
place during normal business hours, we determined that occasional,
regular, scheduled outages in the form of maintenance windows would be
acceptable, and we counted these scheduled outages as planned downtime,
not unplanned downtime.

## Cost

Cost is often the key factor in determining the appropriate availability
target for a service. Ads is in a particularly good position to make
this trade-off because request successes and failures can be directly
translated into revenue gained or lost. In determining the availability
target for each service, we ask questions such as:

* If we were to build and operate these systems at one more nine of
  availability, what would our incremental increase in revenue be?
* Does this additional revenue offset the cost of reaching that level of
  reliability?

To make this trade-off equation more concrete, consider the following
cost/benefit for an example service where each request has equal value:

* Proposed improvement in availability target: 99.9% -> 99.99%
* Proposed increase in availability: 0.09%
* Service revenue: `$1M`
* Value of improved availability: `$1M * 0.0009 = $900`


In this case, if the cost of improving availability by one nine is less
than `$900`, it is worth the investment. If the cost is greater than `$900`,
the costs will exceed the projected increase in revenue.

It may be harder to set these targets when we do not have a simple
translation function between reliability and revenue. One useful
strategy may be to consider the background error rate of ISPs on the
Internet. If failures are being measured from the end-user perspective
and it is possible to drive the error rate for the service below the
background error rate, those errors will fall within the noise for a
given user's Internet connection. While there are significant differences
between ISPs and protocols (e.g., TCP versus UDP, IPv4 versus IPv6),
we've measured the typical background error rate for ISPs as falling
between 0.01% and 1%.

## Other service metrics

Examining the risk tolerance of services in relation to metrics besides
availability is often fruitful. Understanding which metrics are
important and which metrics aren't important provides us with degrees of
freedom when attempting to take thoughtful risks.

Service latency for our Ads system provides an illustrative example.
When Google first launched Web Search, on of the service's key
distinguishing features was speed. When we introduced AdWords, which
displays advertisements next to search results, a key requirement of the
system was that the ads should not slow down the search experience. This
requirement has driven the engineering goals in each generation of
AdWords system and is treated as an invariant.

AdSense, Google's ads system that serves contextual ads in response to
requests from JavaScript code that publishers insert into their
websites, has a very different latency goal. The latency goal for
AdSense is to avoid slowing down the rendering of the third-party page
when inserting contextual ads. The specific latency target, then, is
dependent on the speed at which a given publisher's page renders. This
means that AdSense ads can generally be served hundreds of milliseconds
slower than AdWords ads.

This looser serving latency requirement has allowed us to make many
smart trade-offs in provisioning (i.e., determining the quantity and
locations of serving resources we use), which save us substantial cost
over naive provisioning. In other words, given the relative
insensitivity of the AdSense service to moderate changes in latency
performance, we are able to consolidate serving into fewer geographical
locations, reducing our operational overhead.

## Identifying the Risk Tolerance of Infrastructure Services

The requirements for building and running infrastructure components
differ from the requirements for consumer products in a number of ways.
A fundamental difference is that, by definition, infrastructure
components have multiple clients, often with varying needs.

## Target level of availability

Consider Bigtable
[[Cha06]](https://sre.google/sre-book/bibliography#Cha06), a
massive-scale distributed storage system for structured data. Some
consumer services serve data directly from Bigtable in the path of a
user request. Such services need low latency and high reliability.
Other teams use Bigtable as a repository for data that they use to
perform offline analysis (e.g., MapReduce) on a regular basis. These
teams tend to be more concerned about throughput than reliability. Risk
tolerance for these two use cases is quite distinct.

One approach to meeting the needs of both use cases is to engineer all
infrastructure services to be ultra-reliable. Given the fact that these
infrastructure services also tend to aggregate huge amounts of
resources, such an approach is usually far too expensive in practice. To
understand the different needs of the different types of users, you can
look at the desired state of the request queue for each type of Bigtable
user.

## Types of failures

The low-latency user wants Bigtable's request queues to be (almost
always) empty so that the system can process each outstanding request
immediately upon arrival. (Indeed, inefficient queuing is often a cause
of high tail latency.) The user concerned with offline analysis is more
interested in system throughput, so that user wants request queues to
never be empty. To optimize for throughput, the Bigtable system should
never need to be idle while waiting for its next request.

As you can see, success and failure are antithetical for these sets of
users. Success for the low-latency user is failure for the user
concerned with offline analysis.

## Cost

One way to satisfy these competing constraints in a cost-effective manner
is to partition the infrastructure and offer it at multiple independent
levels of service. In the Bigtable example, we can build two types of
clusters: low-latency clusters and throughput cluster. The low-latency
clusters are designed to be operated and used by services that need low
latency and high reliability. To ensure short queue lengths and satisfy
more stringent client isolation requirement, the Bigtable system can be
provisioned with a substantial amount of slack capacity for reduced
contention and increased redundancy. The throughput clusters, on the
other hand, can be provisioned to run very hot and with less redundancy,
optimizing throughput over latency. In practice, we are able to satisfy
these relaxed needs at at much lower cost, perhaps as little as 10-50%
of the cost of a low-latency cluster. Given Bigtables' massive scale,
this cost savings becomes significant very quickly.

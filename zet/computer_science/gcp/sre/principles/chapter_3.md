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

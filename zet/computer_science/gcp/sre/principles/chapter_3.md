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
accept

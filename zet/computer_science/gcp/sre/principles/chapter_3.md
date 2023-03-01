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

# III - Practices

Put simply, SREs run service-a set of related system, operated for users,
who may be internal or external-and are ultimately responsible for the
health of these services. Successfully operating a service entails a
wide range of activities: developing monitoring systems, planning
capacity, responding to incidents, ensuring the root causes of outages
are addressed, and so on. This section addresses the theory and practice
of an SRE's day-to-day activity: building and operating large
distributed systems.

We can characterize the health of service-in much the same way that
`Abraham Maslow` categorized human needs
[[Mas43]](https://sre.google/sre-book/bibliography#Mas43)-from the most
basic requirements needed for a system to function as a service at all to
the higher levels of function-permitting self-actualization and taking
active control of the direction of the service rather than reactively
fighting fires. This understanding is so fundamental to how we evaluate
services at Google that it wasn't explicitly developed until a number of
Google SREs, including our former colleague Mikey Dickerson,[^41]
temporarily joined the radically different culture of the United States
government to help with the launch of **healthcare.gov** in late 2013
and early 2014: they needed a way to explain how to increase systems'
reliability.

We'll use this hierarchy, illustrated in **Figure 3-1**, to look at the
elements that go into making a service reliable, from most basic to most
advanced.

![Service Reliability Hierarchy.](../../gcp-img/figure_III_1.jpg) Figure
III-1. Service Reliability Hierarchy

### Monitoring

Without monitoring, you have no way to tell whether the service is even
working; absent a thoughtfully designed monitoring infrastructure,
you're flying blind. Maybe

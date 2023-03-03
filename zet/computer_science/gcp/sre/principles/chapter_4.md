# Service Level Objectives

> Written by Chris Jones, John Wilkes, and Niall Murphy
> With Cody Smith
> Edited By Betsy Beyer

It's impossible to manage a service correctly, let alone well, without
understanding which behaviors really matter for that service and how to
measure and evaluate those behaviours. To this end, we would like to
define and deliver a given `level of service` to our users, whether they
use an internal API of a public product.

We use intuition, experience, and an understanding of what users want to
define `service level indicators` (SLIs), `objectives` (SLOs), and
`agreements` (SLAs). These measurements describe basic properties of
metrics and that matter, what values we want those metrics to have, and
how we'll react if we can't provide the expected service. Ultimately,
choosing appropriate metrics helps to drive the right action if
something goes wrong, and also gives an SRE team confidence that a
service is healthy.

This chapter describes the framework we use to wrestle with the problems
of metric modeling, metric selection, and metric analysis. Much of this
explanation would be quite abstract without an example, so we'll use
the Shakespeare service outlined in `Shakespeare: A Sample Service` to
illustrate our main points.

## Service Level Terminology

Many readers are likely familiar with the concept of an SLA, but the
terms `SLI` and `SLO` are also worth careful definition, because in
common use, the term `SLA` is overloaded and had taken on a number of
meanings depending on context. We prefer to separate those meaning for
clarity.

### Indicators

An SLI is a service level `indicator`-a carefully defined quantitative
measure of some aspect of the level of service that is provided.

Most services consider `request latency`-how long it takes to return a
response to a request-as a key SLI. Other common SLIs include the `error
rate`, often expressed as a fraction of all requests received, and
`system throughput`, typically measured in requests per second. The
measurements are often aggregated: i.e., raw data is collected over a
measurement window and then turned into a rate, average, or percentile.

Ideally, the SLI directly measures a service level of interest, but
sometimes only a proxy is available because the desired measure may be
hard to obtain or interpret. For example, client-side latency is often
the more user-relevant metric, but it might only be possible to measure
latency at the server.

Another kind of SLI important to SREs in `availability`, or the fraction
of the time that a service is usable. It is often defined in terms of
the fraction of well-formed requests that succeed, sometimes called
`yield`. (`Durability`-the likelihood that data will be retained over
along period of time-is equally important for data storage systems.)
Although 100% availability is impossible, near-100% availability is
often readily achievable, and the industry commonly expresses
high-availability values in terms of the number of "nines" in the
availability percentage. For example, availabilities for 99% and 99.999%
can be referred to as "2 nines" and "5 nines" availability,
respectively, and the current published target for Google Compute Engine
availability in "three and a half nines"-99.95% availability.

### Objectives

An SLO is a `service level objective`: a target value of range of values
for a service level that is measured by an SLI. A natural structure for
SLOs is thus `SLI <= target`, or `lower bound <= SLI <= upper bound`.
For example, we might decide that we will return Shakespeare search
results "quickly," adopting an SLI that our average search request
latency should be less than 100 milliseconds.

Choosing an appropriate SLO is complex. To begin with, you don't always
get to choose its value! For incoming HTTP requests from the outside
world to your service, the queries per second (QPS) metric is
essentially determined by the desires of your users, and you can't
really set an SLI for that.

On the other hand, you **can** say that you want the average latency per
request to be under 100 milliseconds, and setting such a goal could in
turn motivate you to write your frontend with low-latency behaviors of
various kinds or to buy certain kinds of low-latency equipment. (100
milliseconds is obviously an arbitrary value, but in general lower
latency numbers are good. There are excellent reasons to believe that
fast is better than slow, and that user-experienced latency above
certain values actually drives people away- see "Speed Matters"
[[Bru09]](https://sre.google/sre-book/bibliography#Bru09) for more
details.)

Again, this is more subtle than it might at first appear, in that those
two SLIs-QPS and latency-might be connected behind the scenes: higher
QPS often leads to larger latencies, and it's common for services to
have a performance cliff beyond some load threshold.

Choosing and publishing SLOs to users sets expectations about how a
service will perform. This strategy can reduce unfounded complaints to
service owners about, for example, the service being slow. Without an
explicit SLO, users often develop their own beliefs about desired
performance, which may be unrelated to the beliefs held by the people
designing and operating the service, when users incorrectly believe
that a service will be more available than it actually is (as happened
with Chubby: see `The Global Chubby Planned OUtage`), and under-reliance,
when prospective users believe a system is flakier and less reliable
than it actually is.

### The Global Chubby Planned Outage

> Written by Marc Alvidrez

Chubby [[Bur06]](https://sre.google/sre-book/bibliography#Bur06) is
Google's lock service for loosely coupled distributed systems. In the
global case, we distribute Chubby instances such that each replica is in
a different geographical region. Over time, we found that the failures
of the global instance of Chubby consistently generated service outages,
many of which were visible to end users.

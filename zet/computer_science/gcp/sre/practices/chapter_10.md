# Practical Alerting from Time-Series Data

> Written by Jamie Wilkinson
> Edited by Kavita Guliani

---

> ### May the queries flow, and the pager stay silent.
>
>  Traditional SRE blessing

Monitoring, the bottom layer of the **Hierarchy of Production Needs**,
is fundamental to running a stable service. Monitoring enables service
owners to make rational decisions about the impact of changes to the
service, apply the scientific method to incident response, and of course
ensure their reason for existence: to measure the service's alignment
with business goals (see [Monitoring Distributed
Systems](https://sre.google/sre-book/monitoring-distributed-systems/)).

Regardless of whether or not a service enjoys SRE support, it should be
run in a symbiotic relationship with its monitoring. But having been
tasked with ultimate responsibility for Google Production, SREs develop
a particularly intimate knowledge of the monitoring infrastructure that
supports their service.

Monitoring a very large system is challenging for a couple of reasons:

* The sheer number of components being analyze
* The need to maintain a reasonably low maintenance burden on the
  engineers responsible for the system

Google's monitoring systems don't just measure simple metric, such as
the average response time of an unladen European web server; we also
need to understand the distribution of those response times across all
web servers in that region. This knowledge enables us to identify the
factors contributing to the latency tail.

At the scale our systems operate, being alerted for single-machine
failures is unacceptable because such data is too noisy to be
actionable. Instead we try to build systems that are robust against
failures in the systems they depend on. Rather than requiring management
of many individual components, a large system should be designed to
aggregate signals and prude outliers. We need monitoring systems that
allow us to alert for high-level service objectives, but retain the
granularity to inspect individual components as needed.

Google's monitoring systems evolved over the course of 10 years from the
traditional model of custom scripts that check responses and alerts,
wholly separated from visual display of trends, to a new paradigm. This
new model made the collection of time-series a first-class role of the
monitoring system,and replaced those check scripts with a rich language
for manipulating time-series into charts and alerts.

## The Rise of Borgmon

Shortly after the job scheduling infrastructure Borg
[[Ver15]](https://sre.google/sre-book/bibliography#Ver15) was created in
2003, a new monitoring system-Borgmon-was build to complement it.

## Time-Series Monitoring Outside of Google

This chapter describes the architecture and programming interface of an
internal monitoring tool that was foundational for the growth and
reliability of Google for almost 10 years...but how does that help you,
our dear reader?

In recent years, monitoring has undergone a Bambrian Explosion: Riemann,
Keka, Bosun, and Prometheus have emerged as open source tools that are
very similar to Borgmon's time-series-based alerting. In particular,
Prometheus[^42] shares many similarities with Borgmon, especially when
you compare the two rule languages. The principles of variable
collection and rule evaluation remain the same across all these tools
and provide an environment with which you can experiment, and hopefully
launch into production, the ideas inspired by this chapter.

Instead of executing custom scripts to detect system failures, Borgmon
relies on a common data exposition format; this enables mass data
collection with low overheads and avoids the costs of subprocess
execution and network connection setup. We call this **white-box
monitoring** (see [Monitoring Distributed
Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
for a comparison of white-box and black-box monitoring).

The data is used both for rendering charts and creating alerts, which
are accomplished using simple arithmetic. Because collection is no
longer in a short-lived process, the history of the collected data can
be used for that alert computation as well.

These features help to meet the goal of simplicity described in
[Monitoring Distributed
Systems](https://sre.google/sre-book/monitoring-distributed-systems/).
They allow the system overhead to be kept low so that the people running
the services can remain agile and respond to continuous change in the
system as it grows.

To facilitate mass collection, the metrics format had to be
standardized. An older method of exporting the internal state (known as
**varz**)[^43] was formalized to allow the collection of all metrics
form a single target in one HTTP fetch. For example, to view a page of
metrics manually, you could use the following command: `% curl
https://webserver:80/varz`

```
http_requests 37
errors_total 12
```

A Borgmon can collection from other Borgmon,[^44] so we can build
hierarchies that follow the topology of the service, aggregating and
summarizing information and discarding some strategically at each level.
Typically, a team runs a single Borgmon per cluster, and a pair at the
global level. Some very large services shard below the cluster level
into many **scraper** Borgmon, which in turn feed to the cluster-level
Borgmon.

## Instrumentation of Applications

The **/varz** HTTP handler simply lists all the exported variables in
plain text, as space-separated keys and values, one per line. A later
extension added a mapped variable, which allows the exporter to define
several labels on variable name, and then export a table of values of a
histogram. An example **map-valued** variable looks like the following,
showing 25 HTTP 200 responses and 12 HTTP 500s:

```
http_responses map:code 200:25 404:0 500:12
```

Adding a metric to a program only requires a single declaration in the
code where the metric is needed.

In hindsight, it's apparent that this schema-less textual interface makes
the barrier to adding new instrumentation very low, which is a positive
for both the software engineering and SRE teams. However, this has a
trade-off against ongoing maintenance; the decoupling of the variable
definition from its use in Borgmon rules requires careful changes
management. In practice, this trade-off has been satisfactory because
tools to validate and generate rules have been written as well.[^45]

### Exporting Variable

Google's web roots run deep: each of the major languages used at Google
has an implementation of the exported variable interface that
automagically registers with the HTTP server built into every Google
binary by default.[^46] The instances of the variable to be exported
allow the server author to perform obvious operation like adding an
amount to the current value, setting a key to a specific value, and so
forth. The Go **expvar** library[^47] and its JSON output form have a
variant of the API.

## Collection of Exported Data

To find its targets, a Borgmon instance is configured with a list of
targets using one of the many name resolution methods.[^48] The target
list is often dynamic, so using service discovery reduces the cost of
maintaining it and allows the monitoring to scale.

At predefined intervals, Borgmon fetches the **/varz** URI on each
target, decodes the results, and stores the values in memory. Borgmon
also spreads the collection from each instance in the target list over
the whole interval, so that collection from each target is not in
lockstep with its peers.

Borgmon also records "synthetic" variables for each target in order to
identify:

* If the name was resolved to a host and port
* If the target responded to a collection
* If the target responded to a health check
* What time the collection finished

These synthetic variables make it easy to write rules to detect if the
monitoring tasks are unavailable.

It's interesting that varz is quite dissimilar to SNMP (Simple Network
Management Protocol), which "is designed [...] to have minimal transport
requirements and to continue working when most other network applications
fail" [[Mic03]](https://sre.google/sre-book/bibliography#Mic03).
Scraping targets over HTTP seems to be at odds with this design
principle; however, experience shows that this is rarely an issue.[^49]
The system itself is already designed to be robust against network and
machine failures, and Borgmon allows engineers to write smarter alerting
rules by using the collection failure itself as a signal.

## Storage in the Time-Series Arena

























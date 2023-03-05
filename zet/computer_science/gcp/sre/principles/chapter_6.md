# Monitoring Distributed Systems

> Written by Rob Ewaschuk
> Edited by Betsy Beyer

Google's SRE teams have some basic principles and best practices for
building successful monitoring and alerting systems. This chapter offers
guidelines for what issues should interrupt a human via a page, and how
to deal iwth issues that aren't serious enough to trigger a page.

## Definitions

There's not uniformly shared vocabulary for discussing all topics related
to monitoring. Even without Google, usage of the following terms varies,
but the most common interpretations are listed here.

### Monitoring

Collecting, processing, aggregating, and displaying real-time
quantitative data about a system, such as query counts and types, error
counts and types, processing times, and server lifetimes.

### White-box monitoring

Monitoring based on metrics exposed by the internals of the system,
including logs, interfaces like the Java Virtual Machine Profiling
Interface, or an HTTP handler that emits internal statistics.

### Black-box monitoring

Testing externally visible behavior as a user would see it.

### Dashboard

An application (usually web-based) that provides a summary view of a
service's core metrics. A dashboard may have filters, selectors, and so
on, but is pre-built to expose the metrics most important to its users.
The dashboard might also display team information such as ticket queue
length, a list of high-priority bugs, the current on-call engineer for a
given area of responsibility, or recent pushes.

### Alert

A notification intended to be read by a human and that is pushed to a
system such as a bug or ticket queue, an email alias, or a pager.
Respectively, these alerts are classified as `tickets`, `email
alerts`,[^22] and `pages`.

### Root cause

A defect in a software or human system that, if repaired, instills
confidence that this event won't happen again in the same way. A given
incident might have multiple root causes: for example, perhaps it was
caused by a combination of insufficient process automation, software
that crashed on bogus input, and insufficient testing of the script used
to generate the configuration. Each of these factors might stand alone
as a root cause, and each should be repaired.

### Node and machine

Used interchangeably to indicate a single instance of a running kernel
in either a physical server, virtual machine, or container. There might
be multiple `services` worth monitoring on a single machine. The
services may either be:

* Related to each other: for example, a caching server and a web server
* Unrelated services sharing hardware: for example, a code repository
  and a master for a configuration system like `Puppet` or `Chef`

### Push

Any change to a service's running software or its configuration.

## Why Monitor?

There are many reasons to monitor a system, including:

### Analyzing long-term trends

How big is my database and how fast is it growing? How quickly is my
daily-active user count growing?

### Comparing over time or experiment group

Are queries faster with Acme Bucket of Bytes 2.72 versus Ajax DB 3.14?
How much better is my memcache hit rate with an extra node? Is my
site slower than it was last week?

### Alerting

Something is broken, and somebody needs to fix it right now! Or,
something might break soon, so somebody should look soon.

### Building dashboards

Dashboards should answer basic questions about your service, and
normally include some form of the four golden signals (discussed in `The
Four Golden Signals`).

### Conducting `ad hoc` retrospective analysis (i.e., debugging)

Our latency just shot up; what else happened around the same time?

System monitoring is also helpful in supplying raw input into business
analytics and in facilitating analysis of security breaches. Because
this book focuses on the engineering domains in which SRE has particular
expertise, we won't discuss these applications of monitoring here.

Monitoring and alerting enables a system to tell us when it's broken, or
perhaps to tell us what's about to break. When the system isn't able to automatically fix itself, we want a human to investigate the alert,
determine if there's a real problem at hand, mitigate the problem, and
determine the root cause of the problem. Unless you're performing
security auditing on very narrowly scoped components of a system, you
should never trigger an alert simply because "something seems a bit
weird."

Paging a human is quite expensive use of an employee's time. If an
employee is at work, a page interrupts their workflow. If the employee
is at home, a page interrupts their personal time, and perhaps even
their sleep. When pages occur too frequently, employees second-guess,
skim, or even ignore incoming alerts, sometimes even ignoring a "real"
page that's masked by the noise. Outages can be prolonged because other
noise interferes with rapid diagnosis and fix. Effective alerting
systems have good signal and very low noise.

## Setting Reasonable Expectations for Monitoring

Monitoring a complex application is a significant engineering endeavor in
and of itself. Even with substantial existing infrastructure for
instrumentation, collection, display, and alerting in place, a Google
SRE team with 10-12 members typically has one or sometimes two members
whose primary assignment is to build and maintain monitoring systems for
their service. This number has decreased over time as we generalize and
centralize common monitoring infrastructure, but every SRE team
typically has at least one "monitoring person." (That being said, while
it can be fun to have access to traffic graph dashboards and the like,
SRE teams carefully avoid any situation that requires someone to "stare
at a screen to watch for problems.")

In general, Google has trended toward simpler and faster monitoring
systems, with better tools for `post hoc` analysis. We avoid "magic"
systems that try to learn thresholds or automatically detect causality.
Rules that detect unexpected changes in end-user request rates are one
counterexample; while these rules are still kept as simple as possible,
they give a very quick detection of a very simple, specific, severe
anomaly. Other uses of monitoring data such as capacity planning and
traffic prediction can tolerate more fragility, and thus, more
complexity. Observational experiments conducted over a very long time
horizon (months or years) with a low sampling rate (hours or days) can
also often tolerate more fragility because occasional missed samples
won't hide a long-running trend.

Google SRE has experienced only limited success with complex dependency
hierarchies. We seldom use rules such as, "If I know the database is
slow, alert for a slow database; otherwise, alert for the website being
generally slow." Dependency-reliant rules usually pertain to very stable
parts of our system, such as our system for draining user traffic away
from a data center. For example, "If a data center is drained, then
don't alert me on its latency" is one common data center alerting rule.
Few teams at Google maintain complex dependency hierarchies because our
infrastructure has a steady rate of continuous refactoring

Some of the ideas described in this chapter are still aspirational:
there is always room to move more rapidly from symptom to root cause(s),
especially in every-changing systems. So while this chapter sets out
some goals for monitoring systems, and some ways to achieve these goals,
it's important that monitoring systems-especially the critical path from
the onset of a production problem, through a page to a human, through
basic triage and deep debugging-be kept simple and comprehensible by
everyone on the team.

Similarly, to keep noise low and signal high, the elements of your
monitoring system that direct to a pager need to be very simple and
robust. Rules that generate alerts for humans should be simple to
understand and represent a clear failure.

## Symptoms Versus Causes

Your monitoring system should address two questions: what's broken, and
why?

The "what's broken" indicates the symptom; the "why" indicates a
(possibly intermediate) cause. `Table 6-1` lists some hypothetical
symptoms and corresponding causes.

> Caption: Table 6-1. Example Symptoms and causes

|            Symptom                |              Cause                       |
|            :-----:                |             :-----:                      |
| I'm serving HTTP 500s or 404s     | Database servers are refusing connections|
| My responses are slow             | CPUs are overlaoded by a bogosort, or an Ethernet cable is crimped under a rack, visible as partial packet loss|
| Users in Antartica arent' receiving animated cat GIFs| Your Content Distribution Network hates scientists and felines, and thus blacklisted some client IPs|
| Private content is world-readable | A new software push caused ACLs to be forgotten and allowed all requests |



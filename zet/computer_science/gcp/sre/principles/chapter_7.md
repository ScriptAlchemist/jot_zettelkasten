# The Evolution of Automation at Google

> Written by Niall Murphy with John Looney and Micheal Kacirek
> Edited by Betsy Beyer

```
Beside black art, there is only automation and mechanization.

Federico Garcia Lorca (1898-1936), Spanish poet and playwright
```

For SRE, automation is a force multiplier, not a panacea. Of course,
just **multiplying** force does not naturally change the accuracy of
where that force is applied: doing automation thoughtlessly can create as
many problems as it solves. Therefore, while we believe that
software-based automation is superior to manual operation in most
circumstances, better than either option is a higher-level system design
requiring neither of them-an **autonomous** system. Of to put it another
way, the value of automation comes from both what it does and its
judicious application. We'll discuss both the value of automation and
how our attitude has evolved over time.

## The Value of Automation

What exactly is the value of automation?[^26]

### Consistency

Although scale is an obvious motivation for automation, there are many
other reasons to use it. Take the example of university computing
systems, where many systems engineering folks started their careers.
Systems administrators of that background were generally charged with
running a collection of machines or some software, and were accustomed
to manually performing various actions in the discharge of that duty.
One common example is creating user accounts; others include purely
operational duties like making sure backups happen, managing server
failover, and small data manipulations like changing the upstream DNS
servers' `resolve.conf`, DNS server zone data, and similar activities.
Ultimately, however, this prevalence of manual tasks is unsatisfactory
for both the organizations and indeed the people maintaining systems in
this way. For a start, any action performed by a human or humans
hundreds of times won't be performed the same way each time: even with
the best will in the world, very few of us will ever be as consistent
as a machine. This inevitable lack of consistency leads to mistakes,
oversights, issues with data quality, and yes, reliability problems. In
this domain-the execution of well-scoped, known procedures the value of
consistency is in many ways the primary value of automation

### A Platform

Automation doesn't just provide consistency. Designed and done properly,
automatic systems also provide a `platform` that can be extended,
applied to more systems, or perhaps even spun out for profit.[^27] (The
alternative, no automation, is neither cost effective nor extensible: it
is instead a tax levied on the operation of a system.)

A platform also `centralizes mistakes`. In other words, a bug fixed in
the code will be fixed there once and forever, unlike a sufficiently large
set of humans performing the same procedure, as discussed previously. A
platform can be extended to perform additional tasks more easily than
humans can be instructed to perform them (or sometimes even realize that
they have to be done). Depending on the nature of the test, it can run
either continuously or much more frequently than humans could
appropriately accomplish the task, or at times that are inconvenient
for humans. Furthermore, a platform can export metrics about its
performance, or otherwise allow you to discover details about your
process you didn't know previously, because these details are more easily
measurable within the context of a platform.

### Faster Repairs

There's an additional benefit for systems where automation is used to
resolve common faults in a system (a frequent situation for SRE-created
automation). If automation runs regularly and successfully enough, the
results is a reduced mean time to repair (MTTR) for those common faults.
You can then spend your time on other tasks instead, thereby achieving
increased developer velocity because you don't have to spend time either
preventing a problem or (more commonly) cleaning up after it. As is well
understood in the industry, the later in the product life-cycle a problem
is discovered, the more expensive it is to fix: see [Testing for
Raliability](https://sre.google/sre-book/testing-reliability/).
Generally, problems that occur in actual production are most expensive
to fix, both in terms of time and money, which means that an automated
system looking for problems as soon as they arise has a good chance of
lowering the total cost of the system, given that the system is
sufficiently large.

### Faster Action

In the infrastructural situations where SRE automation tends to be
deployed, humans don't usually react as fast as machines. In most common
cases, where, for example, failover or traffic switching can be well
defined for a particular application, it makes no sense to effectively
require a human to intermittently press a button called "Allow system to
continue to run." (Yes, it is true that sometimes automatic procedures
can end up making a bad situation worse, but that is why such procedures
should be scoped over well-defined domains.) Google has a large amount
of automation; in many cases, the services we support could not long
survive without this automation because they crossed the threshold of
manageable manual operation long ago.

### Time Saving

Finally, time saving is an oft-quoted rationale for automation. Although
people cite this rationale for automation more than other, in many ways
the benefit is often less immediately calculable. Engineers often waver
over whether a particular piece of automation fr code is worth writing,
in terms of effort saved in not requiring a task to be performed
manually versus the effort required to write it.[^28] It's easy to
overlook the fact that once you have encapsulated some task in
automation, anyone can execute the task. Therefore, the time savings
apply across anyone who would plausibly use the automation. Decoupling
operator from operation is very powerful.

Joseph Biranas, and SRE who led Google's datacenter turn up efforts for a
time, forcefully argued:

> "If we are engineering processes and solutions that are not
> automatable, we continue having to staff humans to maintain the system.
> If we have to staff humans to do the work, we are feeding the machines
> with the blood, sweat, and tears of human beings. Think `The Matrix`
> with less special effects and more pissed off System Administrators."

## The Value of Google SRE

All of these benefits and trade-offs apply to use just as much as anyone
else, and Google does have a string bias towards automation. Part of our
preference for automation springs from our particular business
challenges: the products and services we look after are planet-spanning
in scale, and we don't typically have time to engage in the same kind of
machine or service hand-holding common in other organizations.[^29] For
truly large services, the factors of consistency, quickness, and
reliability dominate most conversations about the trade-offs of
performing automation.

Another argument in favor of automation, particularly in the case of
Google, is our complicated yet surprisingly uniform production
environment, described in [The Production Environment at Google, from
the Viewpoint of an
SRE](https://sre.google/sre-book/production-environment/). While other
organizations might have an important piece of equipment without a
readily accessible API, software for which no source code is available,
or another impediment to complete control over production operations,
Google generally avoids such scenarios. We have built APIs for systems
when no API was available from the vendor. Even though purchasing
software for a particular task would have been much cheaper in the short
term, we chose to write our own solutions, because doing so produced APIs
with the potential for much greater long-term benefits. We spent a lot
of time overcoming obstacles to automatic systems management, and then
resolutely developed that automatic system management itself. Given how
Google manages its source code
[[Pot16]](https://sre.google/sre-book/bibliography#Pot16), the
availability of that code for more or less any system that SRE touches
also means that our missions to "own the product in production" is much
easier because we control the entirety of the stack.

Of course, although Google is ideologically bent upon using machines to
manage machines where possible, reality requires some modification of
our approach. It isn't appropriate to automate every component of every
system, and not everyone had the ability or inclination to develop
automation at a particular time. Some essential systems started out as
quick prototypes, not designed to last or to interface with automation.
The previous paragraphs state a maximalist view of our position, but one
that we have been broadly successful at putting into action within the
Google context. In general, we have chosen to create platforms where we
could, or to `position` ourselves so that we could create platforms
over time. We view this platform-based approach as necessary for
manageability and scalability.

## The Use Cases for Automation

In the industry, **automation** is the term generally used for writing
code to solve a wide variety of problems, although the motivations for
writing this code, and the solutions themselves, are often quite
different. More broadly, in this view, automation is
"meta-software"-software to act on software.

As we implied earlier, there are a number of use cases for automation.
Here is a non-exhaustive list of example:

* User account creation
* Cluster turn-up and turn-down for services
* Software of hardware installation preparation and decommissioning
* Roll-outs of new software versions
* Runtime configuration changes
* A special case of runtime config changes: changes to your dependencies

This list could continue essentially **ad infinitum**.

### Google SRE's Use Case for Automation

In Google, we have all of the use cases just listed, and more.

However, within Google SRE, our primary affinity has typically been for
running infrastructure, as opposed to managing the quality of the data
that passes over the infrastructure. This line isn't totally clear-for
example, we care deeply if half of a dataset vanishes after a push, and
therefore we alert on coarse-grain differences like this, but it's rare
for us to write the equivalent of changing the properties of some
arbitrary subset of accounts on a system. Therefore, the context for
our automation is often automation to manage the lifecycle of systems,
not their data: for example, deployments of a service in a new cluster.

To this extent, SRE's automation efforts are not far off what many other
people and organizations do, except that we use different tools to
manage it and have a different focus (as we'll discuss).

Widely available tools like Puppet, Chef, cfengine, and een Perl, which
all provide ways to automate particular tasks, differ mostly in terms of
the level of abstraction of the components provided to help the act of
automating. A full language like Perl provides POSIX-level affordances,
which in theory provide an essentially unlimited scope of automation
across the APIs accessible to the system,[^30] whereas Chef and Puppet
provide out-of-the-box abstractions with which services of other
higher-level entities can be manipulated. The trade-off here is classic:
higher-level abstractions are easier to manage and reason about, but
when you encounter a "leaky abstraction," you fail systemically,
repeatedly, and potentially inconsistently. For example, we often assume
that pushing a new binary to a cluster is atomic; The cluster will
either end up with the old version, or the new version. However,
real-world behaviour is more complicated: that cluster's network can
fail halfway through; machines can fail; communication to the cluster
management layer can fail, leaving the system in an inconsistent state;
depending on the situation, new binaries could be staged but not pushed,
or pushed but not restarted, or restarted but not verifiable. Very few
abstractions model these kinds of outcomes successfully, and most
generally end up halting themselves and calling for intervention. Truly
bad automation systems don't even do that.

SRE has a number of philosophies and products in the domain of
automation, some of which look more like generic rollout tools without
particularly detailed modeling of higher-level entities, and some of
which look more like languages for describing service deployment (and so
on) at a very abstract level. Work done in the latter tends to be more
reusable and be more of a common platform than the former, but the
complexity of our production environment sometimes means that the former
is the most immediately traceable option.

### A Hierarchy of Automation Classes

Although all of these automation steps are valuable, and indeed an
automation platform is valuable in and of itself, in an ideal world, we
wouldn't need externalized automation. In fact, instead of having a
system that has to have external glue logic, it would be even better to
just have a system that needs `no glue logic at all`, not just because
internalization is more efficient (although such efficiency is useful),
but because it has been designed to not need glue logic in the first
place. Accomplishing that involves taking the use cases for glue
logic-generally "first order" manipulations of a system, such as adding
accounts or performing system turnup-and finding a way to handle those
use cases directly within the application.

As a more detailed example, most turnup automation at Google is
problematic because it ends up being maintained separately from the core
system and therefore suffers from "bit rot," i.e., not changing when the
underlying systems change. Despite the best of intentions, attempting to
more tightly couple the two (turnup automation and the core system)
often fails due to unaligned priorities, as product developers will, not
unreasonably, resist a test deployment requirement for every change.
Secondly, automation that is crucial but only executed at infrequent
intervals and therefore difficult to test is often particularly fragile
because of the extended feedback cycle. Cluster failover is one classic
example of infrequently executed automation: failovers might only occur
every few months, or infrequently enough that inconsistencies between
instances are introduced. The evolution of automation follows a path.

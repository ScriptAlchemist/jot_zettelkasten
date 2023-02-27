# Site Reliability Engineering

> How Google runs production systems
>
> Edited by Betsy Beyer, Chris Jones, Jennifer Petoff & Niall Murphy

## Table of Contents

* [Table of Content](#table-of-contents)
* [Foreword](#foreword)
* [Preface](#preface)
* [Part I - Introduction](./introduction/introduction.md)
  * [Chapter 1 - Introduction](./introduction/chapter_1.md)
  * [Chapter 2 - The Production Environment at Google, from the Viewpoint of an SRE](./introduction/chapter_2.md)
* [Part II - Principles](./principles/principles.md)
  * [Chapter 3 - Embracing Risk](./principles/chapter_3.md)
  * [Chapter 4 - Service Level Objectives](./principles/chapter_4.md)
  * [Chapter 5 - Eliminating Toil](./principles/chapter_5.md)
  * [Chapter 6 - Monitoring Distributed Systems](./principles/chapter_6.md)
  * [Chapter 7 - The Evolution of Automation at Google](./principles/chapter_7.md)
  * [Chapter 8 - Release Engineering](./principles/chapter_8.md)
  * [Chapter 9 - Simplicity](./principles/chapter_9.md)
* [Part III - Practices](./practices/practices.md)
  * [Chapter 10 - Practical Alerting](./practices/chapter_10.md)
  * [Chapter 11 - Being On-Call](./principles/chapter_11.md)
  * [Chapter 12 - Effective Troubleshooting](./principles/chapter_12.md)
  * [Chapter 13 - Emergency Response](./principles/chapter_13.md)
  * [Chapter 14 - Managing Incidents](./principles/chapter_14.md)
  * [Chapter 15 - Postmortem Culture: Learning from Failure](./principles/chapter_15.md)
  * [Chapter 16 - Tracking Outages](./principles/chapter_16.md)
  * [Chapter 17 - Testing for Reliability](./principles/chapter_17.md)
  * [Chapter 18 - Software Engineering in SRE](./principles/chapter_18.md)
  * [Chapter 19 - Load Balancing at the Frontend](./principles/chapter_19.md)
  * [Chapter 20 - Load Balancing in the Datacenter](./principles/chapter_20.md)
  * [Chapter 21 - Handling Overload](./principles/chapter_21.md)
  * [Chapter 22 - Addressing Cascading Failures](./principles/chapter_22.md)
  * [Chapter 23 - Managing Critical State: Distributed Consensus for Reliability](./principles/chapter_23.md)
  * [Chapter 24 - Distributed Periodic Scheduling with Cron](./principles/chapter_24.md)
  * [Chapter 25 - Data Processing Pipelines](./principles/chapter_25.md)
  * [Chapter 26 - Data Integrity: What You Read Is What You Wrote](./principles/chapter_26.md)
  * [Chapter 27 - Reliable Product Launches at Scale](./principles/chapter_27.md)
* [Part IV - Management](./management/management.md)
  * [Chapter 28 - Accelerating SREs to On-Call and Beyond](./management/chapter_28.md)
  * [Chapter 29 - Dealing with Interrupts](./management/chapter_29.md)
  * [Chapter 30 - Embedding an SRE to Recover from Operational Overload](./management/chapter_30.md)
  * [Chapter 31 - Communication and Collaboration in SRE](./management/chapter_31.md)
  * [Chapter 32 - The Evolving SRE Engagement Model](./management/chapter_32.md)
* [Part V - Conclusions](./conclusions/conclusions.md)
  * [Chapter 33 - Lessons Learned from Other Industries](./conclusions/chapter_33.md)
  * [Chapter 34 - Conclusion](./conclusions/chapter_34.md)
  * [Appendix A - Availability Table](./conclusions/appendix_a.md)
  * [Appendix B - A Collection of Best Practices for Production Services](./conclusions/appendix_b.md)
  * [Appendix C - Example Incident State Document](./conclusions/appendix_c.md)
  * [Appendix D - Example Postmortem](./conclusions/appendix_d.md)
  * [Appendix E - Launch Coordination Checklist](./conclusions/appendix_e.md)
  * [Appendix F - Example Production Meeting Minutes](./conclusions/appendix_f.md)
  * [Bibliography](./conclusions/bibliography.md)

## Foreword

Google's story is a story of scaling up. It is one of the great success
stories of the computing industry, making a shift towards IT-centric
business. Google was one of the first companies to define what
business-IT alignment meant in practice, and went on to inform the
concepts of DevOps for a wider IT community. This book has been written
by a broad cross-section of the very people who made that transition a
reality.

Google grew at a time when the traditional role of the system
administrator was being transformed. It questioned system
administration, as if to say: we can't afford to hold tradition as an
authority, we have to think anew, and we don't have time to wait for
everyone else to catch up. In the introduction to **Principles of Network
and System Administration**
[[Bur99]](./bibliography/bibliography#Bur99), I claimed that system
administration was a form of human-computer engineering. This was
strongly rejected by some reviewers, who said "we are not yet at the stage
where we can call it engineering." At the time, I felt that the field
had become lost, trapped in its own wizard culture, and could not see a
way forward. Then, Google draw a line in the silicon, forcing that fate
into being. The revised role was called SRE, or Site Reliability
Engineer. Some of my friends were among the first of this new generation
of engineer; they formalized it using software and automation.
Initially, they were fiercely secretive, and what happened inside and
outside of Google was very different: Google's experience was unique.
Over time, information and methods have flowed in both directions. This
book shows a willingness to let SRE thinking come out of the shadows.

Here, we see not only how Google built its legendary infrastructure, but
also how it studied learned, and changed its mind about the tools and
the technologies along the way. We, too, can face up to daunting
challenges with an open spirit. The tribal nature of IT culture often
entrenches practitioners in dogmatic positions that hold the industry back. If
Google overcame this inertia, so can we.

This book is a collection of essays by one company, with a single common
vision. The fact that the contributions are aligned around a single
company's goal is what makes it special. There are common themes, and
common character (software systems) that reappear in several chapters.
We see choices from different perspectives, and know that they correlate
to resolve competing interests. The articles are not rigorous, academic
pieces; they are personal accounts, written with pride, in a variety of
personal styles, and from the perspective of individual skill sets. They
are written bravely, and with an intellectual honesty that is refreshing
and uncommon in industry literature. Some claim "never do this, always
do that," others are more philosophical and tentative, reflecting the
variety of personalities within an IT culture, and how that too plays a
role in the story. We, in turn, read them with the humility of observers
who were not part of the journey, and do not have all the information
about the myriad conflicting challenges. our many questions are the real
legacy of the volume: Why didn't they do **X**? What if they'd  done
**Y**? How will we look back on this in years to come? It is by
comparing our own ideas to the reasoning here that we can measure our
own thoughts and experiences.

The most impressive thing of all about this book is its very existence.
Today, we hear a brazen culture of "just show me the code." A culture of
"ask no questions" has grown up around open source, where community
rather than expertise is championed. Google is a company that dared to
think about the problems for first principles, and the employ top talent
with a high proportion of PhDs. Tools were only components in processes,
working alongside chains of software, people, and data. Nothing here
tells us how to solve problems universally, but that is the point.
Stories like these are far more valuable than the code or designs they
resulted in. Implementations are emphemeral, but the documented
reasoning is priceless. Rarely do we have access to this kind of
insight.

This, then is the story of how one company did it. The fact that it is
many overlapping stories shows us that scaling is far more than just a
photographic enlargement of a textbook computer architecture. It is
about scaling a business process, rather than just the machinery. This
lesson alone is worth its weight in electronic paper.

We do not engage much in self-critical review in the IT world; as such,
there is much reinvention and repetition. For many years, there was only
the USENIX LISA conference community discussing IT infrastructure, plus
a few conferences about operating systems. It is very different today,
yet this book still feels like a rare offering: a detailed documentation
of Google's step through a watershed epoch. The tale is not for
copying--though perhaps for emulation--but it can inspire the next step
for all of us. There is a unique intellectual honesty in these pages,
expressing both leadership and humility. These are stories of hopes,
fears, successes, and failures. I salute the courage of authors and
editors in allowing such candor, so that we, who are not party to the
hands-on experiences, can also benefit from the lessons learned inside
the cocoon.

> Mark Burgess
>
> Author of In Search of Certainty Oslo, March 2016

## Preface

Software engineering has this in common with having children: the labor
**before** the birth is painful and difficult, but the labor **after** the
birth is where you actually spend most of your effort. Yet software
engineering as a discipline spends much more time talking about the
first period as opposed to the second, despite estimates that 40-90% of
the total costs of a system are incurred after birth. The popular
industry model that conceives of deployed, operational software as being
"stabilized" in production, and therefore needing much less attention
from software engineers, is wrong. Through this lens, then, we see that
if software engineering tends to focus on designing and building
software systems, there must be another discipline that focuses on the
**whole** lifecycle of software objects, from inception, through
deployment and operation, refinement, and eventual peaceful
decommissioning. This discipline uses--and needs to use--a wide range of
skills, but has separate concerns from other kinds of engineers. Today,
our answer is the discipline Google calls Site Reliability Engineering.

So what exactly is Site Reliability Engineering (SRE)? We admit that
it's not a particularly clear name for what we do--pretty much every
site reliability engineer at Google gets asked what exactly that is, and
what they actually do, on a regular basis.

Unpacking the term a little, first and foremost, SREs are **engineers**.
We apply the principles of computer science and engineering to the
design and development of computing systems: generally, large distributed
ones. Sometimes, our task is writing the software for those systems
alongside our product development counterparts; sometimes, our task is
building all the additional pieces those systems need, like backups or
load balancing, ideally so they can be reused across systems; and
sometimes, our task is figuring out how to apply existing solutions to
new problems.

Next, we focus on system **reliability**. Ben Treynor Sloss, Google's VP
for 24/7 Operations, originator of the term SRE, claims that reliability
is the most fundamental feature of any product: a system isn't very
useful if nobody can use it! Because reliability is so critical, SREs
are focused on finding ways to improve the design and operation of
systems to make them ore scalable, more reliable and more efficient.
However, we expend effort in this direction only up to a point: when
systems are "reliable enough," we instead invest our efforts in adding
features or building new products.

Finally, SREs are focused on operating **services** built atop our
distributed computing systems, whether those services are planet-scale
storage, email for hundreds of millions of users, or where Google
began, web search. The "site" in our name originally referred to SRE's
role in keeping the **google.com** website running, though we now run
many more services, many of which aren't themselves websites--from
infrastructure such as Bigtable to products for external develops such
as Google Cloud Platform

Although we have represented SRE as a broad discipline, it is no
surprise that it arose in the fast-moving world of web services, and
perhaps in origin owes something to the perculiarities of our
infrastructure. It is equally no surprise that of all the
post-deployment characteristics of software that we could choose to
devote special attention to, reliability is the one we regards as
primary. The domain of web services, both because the process of
improving and changing server-side software is comparatively contained,
and because managing change itself is so tightly coupled with failures
of all kinds, is a natural platform from which our approach might emerge.

Despite arising at Google, and in the web community more generally, we
think that this discipline has lessons applicable to other
communities and other organizations. This book is an attempt to explain
how we do things: both so that other organizations might make use of
what we've learned, and so that we can better define the role and what
the term means. To that end, we have organized the book so that general
principles and more specific practices are separated where possible, and
where it's appropriate to discuss a particular topic with
Google-specific information, we trust that the reader will indulge us in
this and will not be afraid to draw useful conclusions about their own
environment.

We have also provided some orienting material--a description of Google's
production environment and a mapping between some of our internal
software and publicly available software--which should help to
contextualize what we are saying and make it more directly usable.

Ultimately, of course, more reliability-oriented software and systems
engineering is inherently good. However, we acknowledge that smaller
organizations may be wondering how they can best use the experience
represented here: much like security, the earlier you care about
reliability, the better. This implies that even though a small
organization has many pressing concerns and the software choices you
make may differ from those Google made, it's still worth putting
lightweight reliability support in place early on, because it's less
costly to expand a structure later on then it is to introduce one that
is not present. [Management](./management/management.md) contains a number of best practices for
training, communication, and meetings that we've found to work well
for us, many of which should be immediately usable by your organization.

But for sizes between a startup and a multi-national, there probably
already is someone in your organization who is doing SRE work, without
it necessarily being called that name, or recognized as such. Another
way to get started on the path to improving reliability for your
organization is to formally recognize that work, or to find these people
and foster what they do--reward it. They are people who stand on the
cusp between one way of looking at the world and another one: like
Newton, who is sometimes called not the world's first physicist, but
the world's last alchemist.

And taking the historical view, who, then, looking back, might be the
first SRE?

We like to think that Margaret Hamilton, working on the Apollo program
on loan from MIT, had all the significant traits of the first SRE. In
her words, "part of the culture was to learn from everyone and
everything, including from that which one would least expect."

A case in point was when her young daughter Lauren came to work with her
one day, while some of the team were running mission scenarios on the
hybrid simulation computer. As young children do, Lauren went exploring,
and she caused a "mission" to crash by selecting the DSKY keys in an
unexpected way, alerting the team as to what would happen if the
pre-launch program, P01, were inadvertently selected by a real astronaut
during a real mission, during real mid-course. (Launching P01
inadvertently on a real mission would be a major problem, because it
wipes out navigation data, and the computer was not equipped to pilot
the craft with no navigation data.)

With an SRE's instinct, Margaret submitted a program change request to
add special error checking code in the onboard flight software in case
an astronaut should, bu accident, happen to select P01 during flight.
But this move was considered unnecessary by the "higher-ups" as NASA: of
course, that could never happen! So instead of adding error checking
code, Margaret updated the missions specifications documentation to say
the equivalent of "Do not select P01 during flight." (Apparently the
update was amusing to many on the project, who had been told many times
that astronauts would not make any mistakes--after all, they were
trained to be perfect.)

Well, Margaret's suggested safeguard was only considered unnecessary
until the next mission, on Apollo 8, just days after the specification
update. During midcourse on the fourth day of flight with the astronauts
Jim Lovell, William Anders, and Frank Borman on board, Jim Lovell
selected P01 by mistake--as it happens, on Christmas Day--creating much
havoc for all involved. This was a critical problem, because in the
absence of a workaround, no navigation data meant the astronauts were
never coming home. Thankfully, the documentation update had explicitly
called this possibility out, and was invaluable in figuring out how to
upload usable data to recover the mission, with not much time to spare.

As Margaret says, "a thorough understanding of how to operate the
systems was not enough to prevent human errors," and the change request
to add error detection and recovery software to the pre-launch program
P01 was approved shortly afterwards.

Although the Apollo 8 incident occurred decades ago, there is much in
the preceding paragraphs directly relevant to engineers' lives today,
and much that will continue to be directly relevant in the future.
Accordingly, for the systems you look after, for the groups you work in,
or for the organization you're building, please bear the SRE Way in
mind: thoroughness and dedication, belief in the value or preparation
and documentation, and an awareness of what could go wrong, coupled with
a strong desire to prevent it. Welcome to our emerging profession!

> ## How to Read This Book
>
> This book is a series of essay written by members and alumni of
Google's Site reliability Engineering organization. It's much more like
conference proceedings than it is like a standard book by an author or a
small number of authors. Each chapter is intended to be read as a apart
of a coherent whole, but a good deal can be gained by reading on
whatever subject particularly interests you. (If there are other
articles that support or inform the text, we reference them so you can
follow up accordingly.)
>
> You don't need to read in an y particular order, though we'd suggest
al least starting with Chapters The Production Environment at Google,
from the Viewpoint of an SRE and Embracing Risk, which describe Google's
production environment and outline how SRE approaches risk,
respectively. (Risk is, in many ways, the key quality of our
profession.) Reading cover-to-cover is, of course, also useful and
possible; our chapters are grouped thematically, into Principles
([Principles](./principles/principles.md)), Practices
([Practices](./practices/practices.md)), and Management
([Management](./management/management.md)). Each has a small
introduction that highlights what the individual pieces are about, and
references other articles published by Google SREs, covering specific
topics in more detail. Additionally, the companion website the this
book, `https://g.co/SREBook`, has a number of helpful resources.
>
> We hope this will be at least useful and interesting to you as putting
it together was for us.
>
> - The Editors

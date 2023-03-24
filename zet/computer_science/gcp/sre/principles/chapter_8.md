# Release Enginneering

> Written by Dinah McNutt
> Edited by Betsy Beyer and Tim Harvey
> Typed again for study by Justin Bender


Release engineering is a relatively new and fast-growing discipline of
software engineering that can be concisely described as building and
delivering software
[[McN14a]](https://sre.google/sre-book/bibliography#McN14a).
Release engineers have a solid (if not expert)
understanding of source code management, compliers, build
configuration languages, automated build tools, package managers,
and installers. Their skill set includes deep knowledge of
multiple domains: development, configuration management, test
integration, system administration, and customer support.

Running reliable services requires reliable release processes. Site
Reliability Engineers (SREs) need to know that the binaries and
configurations they use are built in a reproducible, automated way to
that releases are repeatable and aren't "unique snowflakes."
Changes to any aspect of the release process should be intentional,
rather than accidental. SREs care about this process from source code to
deployment.

Release engineering is a specific job function at Google. Release
engineers work with software engineers (SWEs) in product development and
SREs to define all the step required to release software-from how the
software is stored in the source code repository, to build rules for
compilation, to ho testing, packaging, and deployment are conducted.

## The Role of a Release Engineer

Google is a data-driven company and release engineering follows suit. We
have tools that report on a host of metrics, such at how much time it
takes for a code change to be deployed into production (in other words,
release velocity) and statistics on what features are being used in
build configuration files
[[Ada15]](https://sre.google/sre-book/bibliography#Ada15). Most of these
tools were envisioned and developed by release engineers.

Release engineers define best practices for using our tools in order to
make sure projects are released using consistent and repeatable
methodologies. Our best practices cover all elements of the release
process. Examples include compiler flags, formats for
build identification tags, and required steps during a build. making sure
that our tools behave correctly by default and are adequately documented
makes it easy for teams to stay focused on features and users, rather
than spending time reinventing the wheel (poorly) when it comes to
releasing software.

Google has a large number of SREs who are charged with safely deploying
products and keeping Google services up and running. In order to make
sure our release processes meet business requirements, release engineers
and SREs work together to develop strategies for canarying changes,
pushing out new releases without interrupting services, and rolling back
features that demonstrate problems.

## Philosophy

Release engineering is guided by an engineering and service philosophy
that's expressed through four major principles, detailed in the
following sections.

### Self-Service Model

In order to work at scale, teams must be self-sufficient. Releases
engineering has developed best practices and tools that allow our
product development teams to control and run their own release
processes. Although we have thousands of engineers and products, we can
achieve a high release velocity because individual teams can decide how
often and when to release new versions of their products. Release
processes can be automated to the point that they require minimal
involvement by the engineers, and many projects are automatically built
and released using a combination of our automated build system and our
deployment tools. Releases are truly automatic, and only require
engineer involvement if and when problems arise.

### High Velocity

User-facing software (such as many components of Google Search) is
rebuilt frequently, as we aim to roll out customer-facing features as
quickly as possible. We have embraced the philosophy that frequent
releases result in fewer changes between versions. This approach makes
testing and troubleshooting easier. Some teams perform hourly build and
then select the version to actually deploy to production from the
resulting pool of builds. Selection is based upon the test results and
the features contained in a given build. Other teams have adopted a
"Push on Green" release model and deploy every build that passes all
tests [[Kle14]](https://sre.google/sre-book/bibliography#Kle14).

### Hermetic Builds

Build tools must allow us to ensure consistency and repeatability. If
two people attempt to build the same product at the same revision number
in the source code repository on different machines, we expect identical
results.[^36] Our builds are hermetic, meaning that they are insensitive
to the libraries and other software installed on the build machine.
Instead, builds depend on known versions of build tools, such as
compilers, and dependencies, such as libraries. The build process is
self-contained and must not rely on services that are external to the
build environment.

Rebuilding older releases when we need to fix a bug in software that's
running in production can be a challenge. We accomplish this task by
rebuilding at the same revision as the original build and including
specific changes that were submitted after that point in time. We call
this tactic `cherry picking`. Our build tools are themselves versioned
based on the revision in the source code repository for the project
being built. Therefore, a project built last month wont use this month's
version of the compiler if a cherry pick is required, because that
version may container incompatible or undesired features.

### Enforcement of Policies and Procedures

Several layers of securities and access control determine who can perform
specific operations when releasing a project. Gated operations include:

* Approving source code changes-this operation is managed through
  configuration files scattered throughout the codebase.
* Specifying the actions to be performed during the release process
* Creating a new release

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

Google is a data-driven company and release engineering follows suit. WE
have tools that report on a host of metrics, such at how much time it
takes for a code change to be deployed into production (in other words,
release velocity) and statistics on what features are being used in
build configuration files 

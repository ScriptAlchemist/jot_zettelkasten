# Wasmtime Reaches 1.0: Fast, Safe and Production Ready!

> Notes from [Lin Clark](https://bytecodealliance.org/articles/wasmtime-1-0-fast-safe-and-production-ready)

As of today, the Wasmtime WebAssembly runtime is now at 1.0! this means
that all of us in the Bytecode Alliance agree that it is fully ready to
use in production.

In truth, we could have called Wasmtime production-ready [more than a
year
ago.](https://github.com/bytecodealliance/rfcs/pull/14#issuecomment-915589031)
But we didn't want to release just any WebAssembly engine. We wanted to
have a super fast and super safe WebAssembly engine. We wanted to feel
really confident with we recommend that people choose Wasmtime.

So to make sure it's production ready for all of you, a number of us in
the Bytecode Alliance have been running Wasmtime in production ourselves
for the past year. And Wasmtime has been doing great in these production
environments, providing a stable platform while also giving us security
and speed wins.

![vetting wasmtime in
production](../../images/wasmtime_1.0/prod-timeline.png)

Here are some of our experiences with the new, improved Wasmtime:

* **Shopify -- 14 months in production**
  - Shopify switched to Wasmtime for another WebAssembly engine in July 2021. With the switch, Shopify saw an `average execution performace improvements of ~50%.`

* **Fastly -- 6 months in production**
  - Fastly switched to Wasmtime from another WebAssembly engine in March
    2022. Fastly also saw a `~50% improvement in execution time`. In
          addition, Fastly saw a `72% to 163% increase in
          requests-per-second` it could serve. Fastly has since served
          `trillions of request` using Wasmtime.

* **DFINITY -- 16 months in production**
  - DFINITY launched the Internet Cimputer blockchain using Wasmtime in
    May 2021. Since then, the Internet Computer has executed `1
    quintillion (10^18) instructions for over 150,000 smart contracts`
    without any production issues.

* **InfinyOn -- 14 months in production**
  - InfinyOn Cloud has been using Wasmtime in production since July
    2021. With Wasmtime, InfinyOne has been able to deliver a greater
          than `5x throughput improvement in end-to-end stream
          processing` when compared to Java-based platforms like Kafka.

* **Fermyon -- 6 months in production**
  - Fermyon's Spin has been using Wasmtime since its release in March
    2022. Since then, Fermyon has found tens of thousands of WebAssembly
          binaries can run in a single Spin instance while keeping
          startup times under a millisecond.

* **Embark -- 2 years in production**
  - Embark has been using Wasmtime in their game engine since 2020.
    Since then, Embark has been impressed with Wasmtimes's exceptional
    stability, safety, and performance, keeping games running at 60 FPS.

* **SingleStore -- 3 months in production**
  - SingleStoreDB Cloud has been using Wasmtime since June 2022 to bring
    developers' code to the data, safely and with speed and scale.

* **Microsoft -- 11 months in preview**
  - Microsoft has had Wasmtime in preview for its WebAssembly System
    Interface (WASI) node pools in Azure Kubernetes Service since
    October 2021.

With all of this experience running it in production without issues, we
feel confident that we can recommend it to you, too.

So I want to tell yo about how we made it super fast and super safe. But
first, why would you want to use a WebAssembly runtime in the first
place?

## Why Use a WebAssembly Runtime?

* WebAssembly was originally created to make code running in the browser
  fast. This meant that you could run much more complex applications,
  like image editing apps or video games, in the browser.
* Each major browser has its own WebAssembly runtime to run these kinds
  of applications.
* WebAssembly opens up lots of use cases outside the browser, too. So in
  these cases, you need to find a standalone WebAssembly runtime like
  Wasmtime.

Here are a few use cases that we have seen people use Wasmtime for.

## Microservices & Serverless

* WebAssembly runtime like Wastime is a perfect fit for Microservices
  and Serverless platforms, where you have independent pieces of code
  that need to scale up and down quickly.
* WebAssembly has a much lower start-up time than other similar
  technologies, such as JS isolates, containers, or VMs.
* For example, it takes the fastest alternative--a JS isolate--about 5ms
  to start up.
* In contrast, it only takes a Wasm instance 5 microseconds to start up.
* WebAssembly's lightweight isolation is great for multi-tenant
  platforms because you can fit so many more workloads on the same
  machine than you can with VMs, containers or other coarse-grained
  isolation technologies.

![Devices connecting to a cloud that holds many instances fo a
WebAssembly module](../../images/wasmtime_1.0/use-case_serverless.png)

## Third Party Plugin Systems

* WebAssembly is great for platforms, where you often want to run 3rd
  party code so you can support many different specific use cases.
* For example, through plug-in marketplaces where developers in the
  platform's ecosystem can share code with users.
* Running this opens up a trust issue--can the platform trust the code
  written by these ecosystem developers?
* With WebAssembly, platforms can run untrusted code but still have some
  security guarantees.
* WebAssembly is sandboxed-by-default and cannot access any resources yo
  don't explicitly hand to it, the platform isn't put at risk.
* The communication between the platform and plug-in is still fast.

![Platform code standing on either side of a sandbox the contains
WebAssembly code. The platform code passes data into the WebAssembly
code, but the WebAssembly code is restructed form using any of the
platform resources.](../../images/wasmtime_1.0/use-case_3rd-party.png)

## Databases, Analytics & Event Streaming

* For database backed applications, WebAssembly can really speed things
  up.
* Database backed applications often waste a lot of time querying a
  database repeatedly, performing some computation based on the returned
  data and then issuing more queries to get more data.
* A was to speed this communication up is to bring the code to the data
  with User Defined Functions (UDFs).
* With UDFs, you can run the code right in the database, getting rid of
  network calls between them.
* With the help of a WebAssembly runtime, databases can use
  WebAssembly-based UDFs to co-locate the code and data.
* This provides fast computation over the data without opening the
  database itself up to security risks.
* These databases can support a lot of different languages in their UDFs
  safely, without the risk of one UDF crashing and taking down the whole
  database.
* UDFs are much more approachable for users who aren't deeply familiar
  with a particular database.

![On one side, a datbase making lots of slow calls to code. On the other
side, the code running directly in the
database](../../images/wasmtime_1.0/use-case_database.png)

## Trusted Execution Environments

* Trusted execution environments (TEEs) are designed for cases where the
  user can't or doesn't what to trust the lower levels for the systems,
  like the hypervisor, kernel, or other system software.## Trusted
  Execution Environments
* The TEEs provides a secure area on the CPU where the TEE-hosted code
  runs, isolated from all of this other software.
* WebAssembly is great for these use cases because it supports lots of
  different languages and is CPU architecture independent.
* This makes it easier to run a TEE across different hardware platforms.

![A CPU with a hypervisor and OS sitting on top of it, plus another,
cordoned off section that contains the TEE wit the WebAssembly
code.](../../images/wasmtime_1.0/use-case_tee.png)

## Portable Clients

* The browser is a good example of a portable client, and many
  applications can just run in the browser.






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
production](./wasmtime_1.0/prod-timeline.png)

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
WebAssembly module](./wasmtime_1.0/use-case_serverless.png)

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
platform resources.](./wasmtime_1.0/use-case_3rd-party.png)

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
database](./wasmtime_1.0/use-case_database.png)

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
code.](./wasmtime_1.0/use-case_tee.png)

## Portable Clients

* The browser is a good example of a portable client, and many
  applications can just run in the browser.
* Sometimes you need a portable client that lives outside of the
  browser--whether that's for performance or for a richer user
  experience.
* For these cases, you can create your own portable client using a
  WebAssembly runtime, like [the BBC did for their
  iPlayer](https://medium.com/bbc-design-engineering/building-a-webassembly-runtime-for-bbc-iplayer-and-enhanced-audience-experiences-7087455808ef).
* The WebAssembly runtime takes care of the portability and making sure
  that guest code can run on different architectures.
* You can focus on the features that you want your client to provide.

![An application window with a WebAssembly logo in it that is going to 3
different devices: a phone, a laptop, and a VR
headset](./wasmtime_1.0/use-case_portable-client.png)

Those are some use cases where you might want to use Wasmtime. Now let's
talk about how we made sure Wasmtime could perform well for these use
cases.

## How we make Wasmtime super fast

For almost all of these use cases, speed makes a difference. That's why
we focus so much on performance.

As [I\'ve talked about
before](https://bytecodealliance.org/articles/making-javascript-run-fast-on-webassembly#two-places-a-js-engine-spends-its-time),
there are two parts of performance that we think about when we're making
optimizations--instantiation and runtime.

If you want all of the details on how we made both of these fast, you
can read [Chris Fallin\'s blog post about Wasmtime
performance](https://bytecodealliance.org/articles/wasmtime-10-performance),
but here's a basic breakdown.

### Instantiation

Instantiation is the time it takes to go from new work arriving (like a
web request, a plugin invocation, or a database query) to having an
instance of the WebAssembly module that is actually ready to run, with
all of its memory and other state prepared for it.

This speed is really important for use cases where you're scaling up and
down quickly, like some microservice architectures and serverless.

As Chris points out in his post. with some of our recent changes:

> Instantiation time of SpiderMonkey.wasm went from about 2
milliseconds... to 5 microseconds, or 400 times faster, not bad!

![A person saying '400 times faster... no
bad!'](./wasmtime_1.0/article-reference-chris.png)

And that's just one example.

We achieved these results mostly bu using two different kinds of
optimizations: virtual memory tricks and lazy initializations. In both
cases, what we're doing it putting off work until it really needs to be
done.

With virtual memory tricks, we don't need to create a new memory every
time we create a new instance. Instead, we depend on operation systems
features to share as much memory between instances as we can, and only
create a new page of memory when one of those instances needs to change
data.

We apply this same kind of thinking to initialization. A module could
have lots of functions and state that it declares but won't use. So we
put off initialization of things like function tables until the function
is actually used. This speeds up start-up, and had the nice benefit of
reducing the overall work that needs to be done in cases where functions
or other state aren't used.

So we got a lot of speed ups in the instantiation phase just by delaying
work until it needed to get done. But we didn't just need to make
instantiation fast. We also need to make runtime fast.

## Runtime

Runtime is how fast the code actually runs once it has started up. This
is especially important when you have long-running code, like portable
clients.

We've been able to improve runtime performance with a variety of
changes. Some of the biggest wins have come from changes we've made to
our complier, Cranelife, which takes the WebAssembly code and turns it
into machine code.

For example, the [new register
allocator](https://cfallin.org/blog/2022/06/09/cranelift-regalloc2/)
makes SpiderMonkey.wasm run ~5% faster. And the new backend framework
(which chooses the best machine instructions to use for blocks of code)
makes SpiderMonkey.wasm run 22% faster than that!

We have some experiments that we're doing here, as well. For example,
we've started work on a new mid-end optimization framework. In early
prototypes, we're already seeing a 13% speed improvement when we run
SpiderMonkey.wasm.

So that's how we drive speed improvements. But what about safety?

## How we make Wasmtime super-safe

Security is a big driver for us in the Bytecode Alliance. We believe
that WebAssembly is uniquely positioned to solve some of the biggest
emerging security threats, and we're committed to making sure the it
does.

We've been pushing for WebAssembly proposals that make it easier for
developers to create secure-by-default applications. But none of that
matters if the runtime that's running the code isn't itself secure.

That's why we put so much effort into the security of Wasmtime itself.

Nick Fitzgerald wrote a [great article about all the different ways we
make sure Wasmtime is
secure](https://bytecodealliance.org/articles/security-and-correctness-in-wasmtime),
and you should read the for more detail, but here are a few examples:

* **We have secured our supply chain using cargo vet.** We're already
  using a memory-safe language, which helps us avoid introducing
  vulnerabilities that attackers could exploit. But that safety doesn't
  necessarily protect us from malicious code that attackers slip into a
  dependency. To protect against this, we're using [cargo
  vet](https://mozilla.github.io/cargo-vet/) to ensure that dependencies
  are manually reviewed by a trusted auditor.
* **We've put a lot of work into fuzzing.** Fuzzing is a way to find
  hard-to-reason-through bugs in your code by just throwing a bunch of
  pseudo-randomly generated input at it. As Nick says, "Our pervasive
  fuzzing is probably the biggest single contributing factor towards
  Wasmtime's code quality. We fuzz because writing tests by hand, while
  necessary, is not enough."
* **We're working on formally verifying security-critical parts of the
  code.** With formal verification, you can actually prove that a
  program does what it's supposed to do, just like a proof in math
  class. the gives us extremely high confidence in the relevant code,
  and helps us pinpoint exactly where the issue is if we accidentally
  introduce new bugs.

![A person shouting 'Fuzz all the
things!'](./wasmtime_1.0/article-reference-nick.png)

So those are some of the ways that we make Wasmtime more secure.

This is something we feel really strongly about--all WebAssembly
runtimes should be following the [best practices that Nick lays
out](https://bytecodealliance.org/articles/security-and-correctness-in-wasmtime)
and more to make sure they are secure. That's the only way we can have
the kind of secure WebAssembly ecosystem we all want.

## What comes after 1.0?

Now that we're at 1.0, we plan to keep a frequent and predictable cycle
of stable releases. We'll release a new version of Wasmtime every month.

Each release of Wasmtime will bump the major version number. This allows
us to keep Wasmtimes's version number in sync with that of the
language-specific embeddings. For example, if you use wasmtime-py 7.0,
you can be sure that you're using Wasmtime 7.0. You can [learn more about the release process here.](https://docs.wasmtime.dev/stability-release.html)

> Copyright 2019-2021 the Bytecode Alliance contributors














# Rust for Big Data and Parallel Processing Applications

## Introduction

A lot has changed since the 2000s, from Java being synonymous with big
data, the latest advancements in CPU, Networking, etc., have given rise
to a new wave of modern language go and rust. In this post, we take a
close look at rust and the promise it holds to support the big data era
and parallel processing applications.

## Why Rust for Big data and Parallel Processing Applications?

iBig Data is a high field growing swiftly, obviously because of its wide
range of use cases and results of such precisions, which results in its
high efficiency, and because of all this, its demand in other fields
too, like in IoT, AI, and Date analytics. There are a lot of products a
projects that have been built using existing tools and architecture and
are very stable. However, that does not mean that we have figured out
everything about it; it does not mean that we do not have any scope for
improvement in this field.

## Challenges with existing system

Most of the tools the are highly accepted and adopted in Big Date are
JVM based. JVM has its limitations.

* JVM abstracts the hardware from the developer. As a result, JVM can
  rarely achieve near-native speed.
* A garbage collector is part of JVM, which helps developers to focus
  more on main business logic and less on memory management. Garbage
  collector with its default configurations works fine but becomes
  disruptive when it gets tweaked a little according to new
  requirements. Some of the tools have developed a by-pass option for
  this inefficiency. For example, Apache Flink has developed its
  off-heap Memory Management. Also, Apache Spark has similar Off-Heap
  Memory Management using Project Tungsten.
* JVM has a huge memory footprint. Due to that, Java is hard to scale
  down. And Linkerd has moved from Scala+Finagle+Netty stack to Rust.
* Java requires Serialization and De-serialization to send the data from
  one process to another processing unit, which is a bit slow and also
  prone to security vulnerabilities.
* Shared memory concurrency is hard to program in Java and is more prone
  to a data race condition.

## Ongoing Projects in Bid Data and Parallel Processing the use Rust

### Apache Spark

Apache Spark is one of the most popular and widely used tools in Big
Data. Which is a JVM based too. So it has all the pros and cons of JVM.
On which,  Andy Grove, in his blog Rust is for Big Data, defined how he
had an experience in this field, and he found some things that could be
the source of more efficiency. He had his daily job of building
distributed data processing jobs with Apache Spark. Also, her shared
some areas where some brilliant engineering had gone into Spark to
handle some of the efficiency issues and how they made Apache Spark (a
JVM based tool) make less use of JVM. Moreover, according to him, if
Apache Spark would have been built in a language like Rust, then it
would be more efficient. So he started an open-source project back later
named `Data Fusion`. 

### DataFusion

So now, DataFusion is an in-memory query engine that uses Apache Arrow
as the memory model. It supports executing SQL queries against CSV and
Parquet files as well as querying directly against in-memory data, and
is being developed by a whole lot of Apache community and Rust
community.

### Weld

There is also a paper-based blog which describes the project Weld, whis
is a Rust based system that generates code for the data analysis
workflow, which runs efficiently in parallel withing the LLVM compiler
framework. According to the researchers from MIT CSAIL, the pipeline
spend more time moving data back and forth between pieces than actually
doing work on it. So, Weld creates a runtime that each library can plug
into, providing a standard method to run critical data across the
pipeline that needs parallelization and optimization

They describe the Weld as a common runtime for data analytics that takes
the disjointed pieces of a modern data processing stack and optimizes
them in concert. So, as numerous projects are running on these systems,
they do not ask developers to change their usage, rather they expect the
framework maintainers and builder to work using Weld. They have also
explained that there was a great deal of trade-off in speed and safety,
which was solved by using Rust as the programming language. Python has
been the second option as a programming language for the freshers that
are trying t enter the field of Data Science and ML and is almost to be
the first option because it is easy to learn and hugely dynamic.

### Python

Yet, a subset of the community believes that Python can get faster and
achieve more effective if the interpreter for the Python language could
be written in Rust. So, there is also an open-source project that is
being developed, `RustPython`. For now, it depends on Python, but the
goal of two things.One, A Python-3 interpreter completely written in
Rust without any binding and second, an interpreter free from any
compatibility hacks. With this, developers could learn Python with the
same ease, and get the benefit form security features that Rust has to
offer.

### Why we need Rust for Big Data and parallel Processing?

Rust is still a young age, despite that, it has a lot to offer because of
its being a low-level language, that interacts with the CPU directly and
has a great defense system against some bugs and all errors, a high
efficiency in threading (which crustaceans named it as `Fearless
Concurrency`), some great unique concepts of `Ownership` and
`Lifetimes`, which allows programmers to define each detail at their own
without repeating themselves.

So, because of all this, it does not require any garbage collection, so
it does not have any. It is built and proven to be highly efficient in
memory management, thread management, and fast in performance. It can
easily integrate wit other languages and have a lot to come in the
future.

There is a lot of work going on in almost every field. Rust is a bit hard
to learn because of its unique concepts and a different approach to OOPS
the most of the languages as Rust follows the 'trait-based' approach to
OOPS, according to which structures are not bound with behaviours,
behaviours are bind with traits which structures can follow for or can
make their trait. This is the new level of abstraction that has provided
a lot to learn in the field of programming. Rust is not perfect, and
there are some downsides too.

Primarily is because of its security feature. Rust takes security in
memory and tread management very seriously, because of which if your
code had a possibility of any security leak, it will not allow you to
compile the code to binary. Rust will  not resolve the security issues
for us, but it will not let us build the binary until we can guarantee
that there is nothing wrong that ca happen. In the latest releases, Rust
has also added a `nightly` compiler, which will build the binary if it
had security leaks too, only if we have added a line in our code, where
we allow code to be unsafe.

However, the community defines it as a feature of the Rust language and
not one of the motive behind developing it, so they said that it would
not be removed from Rust. Instead, the programmer needs to be more
careful about the program they are writing.

### What are Rust Frameworks?

There exist a lot of tools that provide the best solution to well-known
problems or use cases, such as `serde` is a create for Rust that helps,
serialization and de-serialization of the Rust structures, and `rayon`
which could help the developer to program parallel computations, perform
sequential calculations with the API. It will handle the parallel
computation of the action, with a guarantee to provide a data-race free
solution. There also exists several crates that are in progress, still
can be used in a simple solution building.

For example, Actix-Web, which is a web framework for Rust, Tokio, which
is an event-driven, non-blocking I/O platform for writing asynchronous
applications in Rust. The work in a field like big data is still
in progress, but there are some crates that can be used like `JSON data`
that can perform CRUD operations, selecting and sorting, and other
services on json data directly, And `Diesal` - A safe, extensible ORM
and Query Builder for Rust.

### Some Big Data Tools in Rust

There are more than this, but here are a few to check out.

* `Arrow`: Apache Arrow is development platform for in-memory
  analytics.h
* `Polars`: Polars is a blazingly fast DataFrames library implemented in
  Rust using Apache Arrow Format as a memory model.
* `Meilisearch`: Meilisearch is a fast and hyper-relevant search engine.
  It offers a RESTful search API.
* `Data fusion`: DataFusion is an extensible query execution framework
  written in Rust that uses Apache Arrow as its in-memory format. Data
  fusion creates modern, fast, efficient data pipelines, ETL processes
  and database systems.







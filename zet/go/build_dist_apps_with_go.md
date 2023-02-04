# Building Distributed Applications With Go

## Evolution of System Architecture

### Distributed Application

* Client
* Gateway
* Back end Services

### Characteristics of a Distributed System

* Service discovery
* Load balancing
* Distributed tracing and logging
* Service monitoring

### Types of Distributed Systems

* Hub and Spoke
* Peer to Peer
* Message Queues Hybrid

We are going to learn a lot about how there systems work and some challenges of building some of our own.

### Some major topic include:

* Advantages and disadvantages
* Elements that are common
* How services can discover other services
* Monitoring the distributed services

You'll be able to build your own distributed application.

### Prerequisites:

* The GO language
* Go standard library
* How to create web services and applications

For several years most of the primary architecture has been micro services and distributed systems. Let's explore how to create these powerful and flexible systems in Go.

## Introduction to distributed systems

### Evolution of System Architecture

#### Mainframes

Rely on computers for processing power and a terminal to access it.

* Terminal
* Computer

#### Personal Computer

The time when computers got a lot smaller and into the homes of people. Eclipsed mainframes when people though of computing.

#### Web Applications

Shifting back to the server to handle to computation heavy tasks. Sending the tasks to the machines.

* Client
* Server

We now see a split where the client and server share the responsibility to handle the code running. The server handles a little bit more logic and validation.

#### Distributed Application

Turning into more of an API gateway. This gateway is providing an interface for the client to interact with a collection of back end services.

* Client
* Gateway
* Back end services

This provides a lot of flexibility but adds complexity.

What makes up the distributed system and what decisions do we need to make?

What does the system look like?

I want to lay out the landscape that we will be working throughout the rest of this course. We are going to start about talking about the characteristics of a distributed system. What makes it distributed? What are the advantages. The types of the distributed systems. There are multiple ways that we can make a system. So we will look at some options and choose which one might be better for each use case. We will then talk about the Architecture Elements of the distributed systems. And some of the design options we have when we build our own systems

* Characteristics of a Distributed System
* Types of Distributed Systems
* Architectural Elements
* What We'll be building

Going to focus on the underlying idea of building a system in Go. This is not something that would be production ready and scaling. This is starting to get into some of the characteristics of the distributed system.

## Characteristics of a Distributed System

There are four things that come to mind when we thing about distributed systems.

* Service discovery: They need to be able to find each other
* 

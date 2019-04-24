---
title: Introduction to Software Architecture (Monolithic vs. Layered vs. Microservices)
date: 2019-04-24 05:21:18
tags: software architecture
---

In my series [How to Build a Production Web App](), I show you how to design a software architecture for a specific system, but do not go into detail about the various architectures you can choose from.

There are [many architectures to choose from](https://en.wikipedia.org/wiki/Software_architecture#Architectural_styles_and_patterns), but not all of them are "beginner friendly" and sometimes require years of experience to implement correctly.  For example, creating an effective peer-to-peer architecture (i.e. Bitcoin, Bittorrent) is no easy undertaking.

To keep things simple, I will be walking through 3 common architectures that cover a wide variety of use cases.  Unless you have a _specific reason and design experience_ to justify going a different route, one of these three architectures will suffice.

I chose these three because they are natural progressions of each other.  The monolithic is simpler than the layered which is simpler than the microservices.  Sure, there are other useful architectures like Event-Driven, Client-Server, Microkernel, and more, but if you do not understand the 3 below, it would not make sense to attempt any of these advanced architectures anyways.

https://stackoverflow.com/a/45661246/7437737
https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/app01.html#pattern-analysis-summary
https://martinfowler.com/bliki/MonolithFirst.html

**1. Monolithic Architecture**

A monolithic architecture describes an application where everything is condensed into one codebase.  You may have different parts of the application split into folders, but any change to any part of the codebase will affect the rest.

If you have every taken tutorials online that teach you how to build a web application, you have most likely built a monolithic application.  

[Here is a very basic monolithic software architecture](https://github.com/zachgoll/monolithic-architecture-example-app).

A monolithic architecture describes an architecture where all of the following components are bunched into one codebase: 

* Views 
* Application/Business Logic 
* Data Access/Database

Each of these areas are separated in the application, but it is still a monolithic architecture because a change to any part of the application will require: 

1. The entire application be restarted 
2. Changes must be made in more than one part of the app

Although this architecture may seem ineffective, not all industry professionals would write it off.  Martin Fowler, a thought leader in this space [advocates for the use of monolithic architectures](https://martinfowler.com/bliki/MonolithFirst.html) at the beginning of most projects that will eventually become n-tiered or microservices.  He does not advocate for monolithic architecture as an ending state of an app, but highlights that many of the effective microservice applications he has seen _started as monolithic architectures_ and was later refactored.

**2. Layered Architecture (also called "n-tiered")**

_All code mentioned below is stored in my [layered architecture repository on Github](https://github.com/zachgoll/layered-architecture-example-app)_

**3. Microservices Architecture**

_All code mentioned below is stored in my [microservices architecture repository on Github](https://github.com/zachgoll/microservices-architecture-example)_
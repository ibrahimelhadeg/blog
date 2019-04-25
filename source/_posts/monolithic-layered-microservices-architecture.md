---
title: Introduction to Software Architecture (Monolithic vs. Layered vs. Microservices)
date: 2019-04-24 05:21:18
tags: software architecture
---

If you are coming from part 4 of my series [How to Build a Production Web App](), you should have a good idea of why we need software architecture in our projects: 

1. 

There are [many architectures to choose from](https://en.wikipedia.org/wiki/Software_architecture#Architectural_styles_and_patterns), but not all of them are "beginner friendly" and sometimes require years of experience to implement correctly.  For example, creating an effective peer-to-peer architecture (i.e. Bitcoin, Bittorrent) is no easy undertaking.

To keep things simple, I will be walking through 3 common architectures that cover a wide variety of use cases.  Unless you have a _specific reason and design experience_ to justify going a different route, one of these three architectures will usually suffice.

I chose these three because they are natural progressions of each other.  The monolithic is simpler than the layered which is simpler than the microservices.  There are other useful architectures like Event-Driven, Client-Server, Microkernel, and more, but if you do not understand the 3 below, it would not make sense to attempt any of these advanced architectures anyways.

https://stackoverflow.com/a/45661246/7437737
https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/app01.html#pattern-analysis-summary
https://martinfowler.com/bliki/MonolithFirst.html

## Monolithic Architecture

A monolithic architecture describes an application where everything is condensed into one codebase.  You may have different parts of the application split into folders, but changes to any part of the codebase will affect the other elements.

If you have every taken tutorials online that teach you how to build a web application, you have most likely built a monolithic application.  It is by far the easiest to conceptualize starting out.  

A monolithic architecture describes an architecture where all of the following components are bunched into one codebase:

* Views (i.e. HTML, CSS, Javascript)
* Application/Business Logic (i.e. ExpressJS)
* Data Access/Database (i.e. MongoDB)

Although this architecture may seem ineffective, not all industry professionals would write it off.  Martin Fowler, a thought leader in this space [advocates for the use of monolithic architectures](https://martinfowler.com/bliki/MonolithFirst.html) in specific situations.  

**2. Layered Architecture (also called "n-tiered")**

_All code mentioned below is stored in my [layered architecture repository on Github](https://github.com/zachgoll/layered-architecture-example-app)_

**3. Microservices Architecture**

_All code mentioned below is stored in my [microservices architecture repository on Github](https://github.com/zachgoll/microservices-architecture-example)_
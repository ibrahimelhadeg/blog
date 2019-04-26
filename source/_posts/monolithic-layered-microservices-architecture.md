---
title: Introduction to Software Architecture (Monolithic vs. Layered vs. Microservices)
date: 2019-04-24 05:21:18
tags: software architecture
---

If you are coming from part 4 of my series [How to Build a Production Web App](), you should have a good idea of why we need software architecture in our projects: 

1. 

## Introduction

In this post, we will answer the following 3 questions: 

1. **Why do we need a software architecture?**
2. **Monolithic Architecture**
3. **Layered Architecture**
4. **Microservices Architecture**
5. **Other Architectures**

If you are a self-taught developer, new to the industry, or something of the sort, the concept of "software architecture" is intimidating.  Enterprise architects get paid lots of money to do what they do because **architecting a quality software is difficult and requires experience**.

You may have the following concerns: 

* How am I supposed to architect a solution with little prior experience?
* There are so many architectures and design patterns.  What if I choose the wrong one?
* How can I create an entire architecture without knowing all the details about the code I'm going to write?

Throughout this post, we'll work through these concerns and figure out what this architecture thing is all about.

## Why do we need Software Architecture?

"Any intelligent fool can make things bigger, more complex, and more violent. It takes a touch of genius—and a lot of courage to move in the opposite direction"

From E.F. Schumacher's book _Small is Beautiful_, this quote embodies a lot of what architecting software means.  As a developer, it is always more fun to solve a complex problem in a complex way.  It's more fun to create a Rube Goldberg Machine than walking from point A to point B and dropping the marble in the cup.

Unfortunately, as a developer and architect, you get no additional bonus points for indirectly solving problems.  You make the real money by solving a complex problem in a simple way.

**Designing software architecture is about arranging components of a system to best fit the desired attributes of the system.**

* The user cares that your system is fast, reliable, and available
* The project manager cares that the system is delivered on time and on budget
* The CEO cares that the system contributes incremental value to his/her company
* The head of security cares that the system is protected from malicious attacks
* The application support team cares that the system is easy to understand and debug

There is no way to appease every request without sacrificing the quality of the system.  Therefore, when designing software architecture, you must decide _which quality attributes_ matter most for the given business problem.  Below are a few examples of quality attributes: 

* Performance - how long do you have to wait before that spinning "loading" icon goes away?
* Availability - what percentage of the time is the system running?
* Usability - can the users easily figure out the interface of the system?
* Modifiability - if the developers want to add a feature to the system, is it easy to do?
* Interoperability - does the system play nicely with other systems?
* Security - does the system have a secure fortress around it?
* Portability - can the system run on many different platforms (i.e. Windows vs. Mac vs. Linux)? 
* Scalability - if you grow your userbase rapidly, can the system easily scale to meet the new traffic?
* Deployability - is it easy to put a new feature in production?
* Safety - if the software controls physical things, is it a hazard to real people?

Depending on what software you are building or improving, certain attributes may be more critical to success.  If you are a financial services company, the most important quality attribute for your system would probably be _security_ (a breach of security could cause your clients to lose millions of dollars) followed by _availability_ (your clients need to always have access to their assets).

If you are a gaming or video streaming company (i.e. Netflix), your first quality attribute is going to be _performance_ because if your games/movies freeze up all the time, nobody will play/watch them.

As you can see, the process of building software architecture is not about finding the best tools and the latest technologies.  It's about **delivering a system that works how it was intended to work**.  But why do we need an architecture to do this?

According to the book _[Software Architecture in Practice](https://amzn.to/2X1Uoaq)_, there are 13 reasons why Software Architecture is important for the success of a project, but here are a few highlights: 

* Architecture enables quality attributes (as discussed above)
* Architecture enables communication among stakeholders
* Architecture focuses on the assembly rather than creation of components
* Architecture restricts design choices, enabling creativity in other areas

The book points out that design decisions made at the beginning of a project have a **disproportionate weighting** and restrict the ability to change certain areas of the software later on, so it is important to spend time to understand the requirements of the software and design it accordingly.

This is where an experienced architect has an advantage over a novice.  They can see much further into the future and anticipate how certain design decisions will impact the system.  

For those of us (including myself) not so experienced in designing architecture, we must accept that things won't be perfect and **design it anyway**.  Yes, revisions will have to be made, but what other choice is there?

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
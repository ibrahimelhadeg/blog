---
title: Build a Production Web App Part 4 - Architecture Design
date: 2019-04-10 12:30:18
tags:
---

Welcome to part 4 of the "Build a Production Web App" series.  If you are new here, I suggest starting from the beginning.

[Go To Part 1](https://zachgoll.github.io/blog/2019/build-production-web-app-part-1/)

In this part, we will be completing the software architecture portion of the design document introduced in the previous post.

## Introduction

In this post, we will answer the following 3 questions: 

**1. Why do we need a software architecture?**
**2. What architectures are available, and how do we choose one?**
**3. How do we actually build our chosen architecture?**

If you are a self-taught developer, new to the industry, or something of the sort, the concept of "software architecture" is intimidating.  Enterprise architects get paid lots of money to do what they do because **architecting a quality software is difficult and requires experience**.

You may have the following concerns: 

* How am I supposed to architect a solution with little prior experience?
* There are so many architectures and design patterns.  What if I choose the wrong one?
* How can I create an entire architecture without knowing all the details about the code I'm going to write?

Throughout this post, we'll work through these concerns and figure out what this architecture thing is all about.

## Why do we need Software Architecture?

"Any intelligent fool can make things bigger, more complex, and more violent. It takes a touch of genius—and a lot of courage to move in the opposite direction"

From E.F. Schumacher's book _Small is Beautiful_, this quote embodies a lot of what architecting software means.  As a developer, it is always more fun to solve a complex problem in a complex way.  It's more fun to create a Rube Goldberg Machine than walking from point A to point B and dropping the marble in the cup.

Unfortunately, as a developer and architect, you get no additional bonus points for indirectly solving problems.  You get bonus points for creating an _effective system_.

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

For those of us not so experienced in designing architecture, we must accept that things won't be perfect and **design it anyway**.

But where do we even start?

## What Architectures are Available, and How do we Choose?

The word "Architecture" is a broad term.  It could refer to the structure of a skyscraper, the design of a codebase, the makeup of your body...  The word also could be used on different levels.  A skyscraper building has an external architecture, the architecture within each floor, and even the architecture which describes the electrical wiring and plumbing systems of the building.  Software is no different.  Within a software system, we can define many layers of architecture at various levels of detail.

[Mark Richards](http://www.wmrichards.com/), an established author and software architect divides architecture into four categories: 

1. Enterprise Architecture - the framework for high-level architecture (service oriented, event driven, resource oriented)
2. Application Architecture - how the components of an enterprise architecture are designed
3. Integration Patterns - how the components of a system connect with each other
4. Design Patterns - patterns used for actually coding the parts of a system.  The book _Design Patterns: Elements of Reusable Object-Oriented Software](https://amzn.to/2Da9rr8)_ defines 3 subsets of design patterns (creational patterns, structural patterns, and behavior patterns).

Just like a skyscraper, software has many layers; all of which require design/architecture decisions.

It is easy to become intimidated by all the fancy verbage and fall into a spiral of analysis paralysis, but in the end, software architecture is not about fancy names and classifications.  Software architecture is about choosing the right design patterns that will enable your code to run effectively and expose the quality attributes (scalability, performance, etc.) that you desire in your system.

Although we could get into all four of the layers described by Richards above, the project that we are working on does not require it.  The golf practice application we are building is not architectually complex, and we can break it down into three phases: 

1. **Design the Main Architecture** - This takes into consideration everything including firewalls, load balancers, application components, data stores, etc.  It might also show how the system will connect with an existing system if you are building it for a larger enterprise.
2. **Design the Application Architecture** - This is specifically geared towards the design of the application itself.
3. **Design the Components** - This is where we decompose the application architecture and use design patterns.  In many cases, this is the part where you start coding.

### Main Architecture 

The main architecture is tricky because there is no guidebook telling you how to put it together.  There is no Google search that can explain how to integrate a Vendor software into your company's existing data supply chain and legacy infrastructure.

The goal of the main architecture is to capture all the pieces and define as best as you can how they will talk and relate to each other.  There is no book that can tell you how to get this part right; only experience.

In a later section when I design the main architecture for this golf application we are building, you will get a sense of how to approach this problem.

### Application Architecture

There are [many architectures to choose from](https://en.wikipedia.org/wiki/Software_architecture#Architectural_styles_and_patterns), but not all of them are "beginner friendly" and sometimes require years of experience to implement correctly.  For example, creating an effective peer-to-peer architecture (i.e. Bitcoin, Bittorrent) is no easy undertaking.

To keep things simple, I will be walking through 3 common architectures that cover a wide variety of use cases.  Unless you have a _specific reason and design experience_ to justify going a different route, one of these three architectures will suffice.

I chose these three because they are natural progressions of each other.  The monolithic is simpler than the layered which is simpler than the microservices.  Sure, there are other useful architectures like Event-Driven, Client-Server, Microkernel, and more, but if you do not understand the 3 below, it would not make sense to attempt any of these advanced architectures anyway.

https://stackoverflow.com/a/45661246/7437737
https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/app01.html#pattern-analysis-summary
https://martinfowler.com/bliki/MonolithFirst.html

**1. Monolithic Architecture**

 

**2. Layered Architecture (also called "n-tiered")**
**3. Microservices Architecture**

### Common Design Patterns 

## Choosing the Architecture

For this project, I have chosen to use the layered architecture and several object oriented design patterns.  I came to these choices by first deciding which quality attributes were important to me: 

* I wanted an architecture that was easy to conceptualize (mainly for the purpose of the blog and YouTube series).
* I wanted an architecture that had high modifiability because I plan on adding many features past the first version release.
* I wanted an architecture that maximized usability.  My users should not have to go through any complexity to get started.

A layered architecture is easy to understand and arguably the most common architecture of them all.  The isolation of layers will enable future iterations of the software to be seamless, and the simplicity of the design will maximize usability.

## Building the Architecture

It is finally time to build the architecture for the golf practice application!

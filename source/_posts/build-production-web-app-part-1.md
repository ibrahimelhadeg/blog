---
title: Build a Production Web App Part 1 - Introduction
date: 2019-03-10 07:38:32
tags:
---

There is no shortage of tutorials on how to build a web application, but there is nothing that I have found which covers the process of building a production-ready application from start to finish (yes, everything!). This post/video series is for those individuals and/or startups who want to get it right the first time. Instead of hacking something together and later finding out you have secret keys in your repositories, unreadable and long functions, failing test suites, bad code syntax, and no scalability, why not just get it right the first time?

Maybe this is too lofty a goal, but the purpose of this series is to do just that. Get it right the first time. Throughout the next few months, I will be building a Golf Practice and Stats application for my site, [The DIY Golfer](https://www.thediygolfer.com).

This is not your average tutorial where I build the classic Todo List Application to demonstrate programming concepts.  This tutorial is a real life example of what goes into building a useful software product.

Since this application is actually a product that I am building for users of my website, I will be keeping the source code private for obvious reasons.  That said, you will see a large part of the version 1 throughout the tutorial, and this will be under the MIT license. Therefore, you are free to use what you see however you would like under the condition that you provide the [license](https://opensource.org/licenses/MIT) in your code with "Copyright 2019 Zach Gollwitzer" as the first line, and your name as the second.

The goal of this series is not only to teach you the process of going from _idea_ to _product_, but also to teach the software components that go into a production system.

## Prerequisites

This series is for anyone who is looking to build a web application product but does not have unlimited funding to hire multiple areas of expertise. This is not a tutorial on how to build a POC (proof of concept), but rather how to build a production-ready application _based on_ a POC (or idea).

For this project, we will be touching on each of the following skills, programming concepts/paradigms, and programming best practices.  Throughout the series, you will see <span class="ext-topics">[prerequisite markers]()</span> for topics that you must understand to move forward.  If you reach a prerequisite marker and do not understand or need a refresher, click the link and you will be taken to a tutorial that gives you just enough information about that topic to move forward. 

### Skills 

- Basic programming skills
- HTML/CSS/JS skills
- The MEAN programming stack 
    - NodeJS (Javascript) [video]() | [post]()
    - ExpressJS (Javascript)
    - MongoDB/Mongoose (Javascript)
    - Angular 5+ (Typescript)
- Unit/QA/Integration Testing 
    - SinonJS
    - Chai
    - Mocha
- Cloud computing/storage/networking
    - Firewalls 
    - Load Balancers 
    - DNS 
    - Reverse Proxies 
    - Subnets 
    - Storage types 
    - Virtual machines
- Version Control (Git)
- Automated Infrastructure
- Provisioning Infrastructure
- Continuous Delivery
- Debugging code in Visual Studio Code Editor

### Programming Concepts/Paradigms

- Imperative vs. declarative programming (object oriented programming, functional, etc.)
- Architecture design (i.e. layered, MVC, microservices, pub sub, etc.)
    - Principles of architecture design (i.e. separation of concerns)
- Asynchronous Javascript programming (Promises and Observables)
- Continuous Delivery (Agile) vs. Waterfall project planning
- Automated infrastructure and deployment

### Best Practices 

- Code Syntax (linting)
- Git workflow (branching strategy)
- Logging 
- Debugging (with Visual Studio Code)

## How to use this Series

This series is going to be LONG. It will take you weeks to finish if you follow it all the way through, and that is how it should be. We are building a production application here, which is **not an easy task**.

Here are a few tips for going through the series: 

* If you reach a post that does not apply to your situation, **skip it!!**  No need to waste time building your product.
* Do not try to learn all the prerequisites _prior_ to the series.  This is "on the job learning", and therefore, you should only visit the <span class="ext-topics">[prerequisite tutorials]()</span> when you reach a point where they are necessary to move forward.
* Skim the entire series prior to diving in

Best of luck, and if you have any questions, please leave them on the YouTube video that corresponds to the tutorial your question is about!

[Go to next post](http://zachgoll.github.io/blog/2019/build-production-web-app-part-2/)
---
title: Build a Production Web App Part 1 - Introduction
date: 2019-03-10 07:38:32
tags:
    - series
---

There is no shortage of tutorials on how to build a web application, but there is nothing that I have found which covers the process of building a production-ready application from start to finish (yes, everything!). This post/video series is for those individuals and/or startups who want to get it right the first time. Instead of hacking something together and later finding out you have secret keys in your repositories, unreadable and long functions, failing test suites, bad code syntax, and no scalability, why not just get it right the first time?

This is not your average tutorial where I show you how to build the classic Todo List Application.  This tutorial is a **real life example** of what goes into building a useful software product.  In this series, I will be building a Golf Practice and Stats application for my site, [The DIY Golfer](https://www.thediygolfer.com).  It will be released to **real users**.

Since this application is actually a product that I am building for users of my website, I will be keeping the source code private.  That said, you will see a large part of the version 1 throughout the tutorial, and this will be under the MIT license. Therefore, you are free to use what you see however you would like under the condition that you provide the [license](https://opensource.org/licenses/MIT) in your code with "Copyright 2019 Zach Gollwitzer" as the first line, and your name as the second.

## What's In it for YOU (Goal of the Series)

This series is meant to teach you the _entire process_ it takes to create a production-ready web (and mobile) application from the ground up.  We are going from _idea to product_.  By the end of the series, you will not only be able to code a production-ready application; you will also understand: 

1. _Why_ we chose certain technologies, architectures, designs, and other pieces of the application
2. _Who_ in a company is responsible for each part of this application? (i.e. If I was not doing this alone, what kind of team would you have to assemble to create it?)
3. _What_ skills are required to build a production-ready application?

## Prerequisites

This series is for anyone who is looking to build a web application product but does not have unlimited funding to hire multiple areas of expertise.  This is not a tutorial on how to build a POC (proof of concept), but rather how to build a production-ready application _based on_ a POC (or idea).

For this project, we will be touching on each of the following **skills**, programming **concepts/paradigms**, and programming **best practices**.  Throughout the series, you will see <span class="ext-topics">[prerequisite markers](/blog/tutorial-in-progress)</span> for topics that you must understand to move forward.  If you reach a prerequisite marker and do not understand or need a refresher, click the link and you will be taken to a tutorial that gives you just enough information about that topic to move forward.

**Please note that this is a work in progress, and some of the prerequisite markers will not have live links yet.  In these cases, you may need to consult our best friend--Google, to move forward.**

### Skills 

- Basic programming skills
- HTML/CSS/JS skills
- The MEAN programming stack 
    - NodeJS (Javascript)
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

* If you reach a post that does not apply to your situation, **skip it!!**  I'm not here to waste your time.
* Do not try to learn all the prerequisites _prior_ to the series.  This is "on the job learning", and therefore, you should only visit the <span class="ext-topics">[prerequisite tutorials](/blog/tutorial-in-progress)</span> when you reach a point where they are necessary to move forward.

Best of luck, and if you have any questions, please leave them on the YouTube video that corresponds to the tutorial your question is about!

Next post in series: [Statement of Work](/blog/2019/build-production-web-app-part-2)
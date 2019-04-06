---
title: Build a Production Part 1 - Introduction
date: 2019-03-10 07:38:32
tags:
---

Some might say that I am the last person that should be making this tutorial, but that is exactly why I am making it. There is no shortage of tutorials on how to build a web application, but there is nothing that I have found which covers the process of building a production-ready application from start to finish (yes, everything!). This post/video series is for those individuals and/or startups who want to get it right the first time. Instead of hacking something together and later finding out you have secret keys in your repositories, unreadable and long functions, failing test suites, bad code syntax, and no scalability, why not just get it right the first time?

Maybe this is too lofty a goal, but the purpose of this series is to do just that. Get it right the first time. Throughout the next few months, I will be attempting to build a Golf Practice and Stats application for my site, [The DIY Golfer](https://www.thediygolfer.com).

This is not your average tutorial where I build the classic Todo List Application to demonstrate programming concepts.  This tutorial is a real life example of what goes into building a useful software product.

Since this application is actually a product that I am building for users of my website, I will be keeping the source code private for obvious reasons.  

That said, you will see a large part of the version 1 throughout the tutorial, and this will be under the MIT license. Therefore, you are free to use what you see however you would like under the condition that you provide the following license in your code.

```
MIT License

Copyright (c) 2019 Zach Gollwitzer
Copyright (c) 2017 Thinkster

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

The goal of this series is not to teach you exactly how to build a particular application (although we will be coding a lot throughout), but rather to document the entire process and explain what it takes to go from _idea_ to _product_.

## Prerequisites

This series is for anyone who is looking to build a web application product but does not have unlimited funding to hire multiple areas of expertise. This is not a tutorial on how to build a POC (proof of concept), but rather how to build a production-ready application _based on_ a POC (or idea).

For this project, we will be touching on each of the following skills, programming concepts/paradigms, and programming best practices.  I will do my best to explain each item as we implement them, but some topics (i.e. Angular, NodeJS) are too involved to embed within the tutorial.  For those too complex to include within the flow of programming this production application, I have created standalone tutorials that wil get you up to speed.

### Skills 

You will need the following skills for this tutorial. I have written (also made videos for) crash courses on many of these, so if there are any areas you do not feel comfortable with, below are tutorials that I have created to assist you.  My recommendation is that you take them on an "as-needed" basis (i.e. do not take a tutorial unless you are going to immediately apply the concepts learned within).

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

## Technology Stack

For this series, I will be using the following technology stack.  We will dive further into the details behind this in a later post, but I wanted to preview the stack so that potential readers can decide whether this series is right for them.

- MEAN Stack (MongoDB, ExpressJS, Angular 5+, NodeJS)
- Digital Ocean for hosting
- Sass / Bootstrap for CSS
- Gitlab for source code management
- Gitlab continuous delivery tool
- Terraform for automated infrastructure

## How to use this Series

This series is going to be LONG. It will take you weeks to finish if you follow it all the way through, and that is how it should be. We are building a production application here, which is **not an easy task**.

For most people viewing this, you probably already have an idea where to go with various parts of the tutorial, so feel free to skip around to the sections you need help with. If you are taking this tutorial solely for learning purposes, I suggest going through each and every lesson!

Best of luck! If you have questions, feel free to leave them on the YouTube video that corresponds to the post you are reading.
---
title: Build a Production Web App (MEAN Stack) - Part 1
date: 2019-03-10 07:38:32
tags:
---

Some might say that I am the last person that should be making this tutorial, but that is exactly why I am making it. There is no shortage of tutorials on how to build a web application, but there is nothing that I have found which covers the process of building a production-ready application. This post/video series is for those individuals and/or startups who want to get it right the first time. Instead of hacking something together and later finding out in an extensive code review that you have secret keys in checked into your repositories, unreadable and long functions, failing test suites, and no scalability, why not just get it right the first time?

Maybe this is too lofty a goal, but the purpose of this series is to do just that. Get it right the first time. Throughout the next few months, I will be attempting to build a Golf Practice and Stats application for my site, [The DIY Golfer](https://www.thediygolfer.com). The scope of the tutorial will cover the following application features:

- Username and password user authentication
- Integration with GHIN (USGA) handicap service via widgets (they do not have an official API)
- Round stats entry
- Analytics dashboard

Since this application is actually a product that I am building for users of my website, I will be keeping the source code private. You will see a large part of the first version here throughout the tutorial, and this will be under the MIT license. Therefore, you are free to use what you see however you would like under the condition that you provide the following license in your code.

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

The goal of this series is not to teach you exactly how to build a particular application, but rather to document the entire process and explaining what it takes to go from _idea_ to _product_.

## Prerequisites

This series is for anyone who is looking to build a web application product but does not have unlimited funding to hire multiple areas of expertise. This is not a tutorial on how to build a POC (proof of concept), but rather how to build a production-ready application _based on_ a POC (or idea).

You will need the following skills for this tutorial. I have written (also made videos for) crash courses on many of these, so if there are any areas you do not feel comfortable with, just go through the tutorial for that given topic when you reach it. The tutorials that I have written for each are meant for those who already know how to code and just need either a refresher or crash course on the topic.

- NodeJS (Javascript)
- ExpressJS
- Angular 5+ (Typescript)
- MongoDB
- Mongoose (MongoDB for Express)
- SinonJS, Mocha, Chai for Unit Testing
- Asynchronous javascript coding - Promises
- Asynchronous javascript coding - rxjs Observables
- Cloud networking (firewalls, load balancers, NICs, LANs, etc.)
- Git
- Automated Infrastructure
- Provisioning Infrastructure
- Continuous Delivery
- Debugging code in Visual Studio Code Editor

## Technology Stack

For this series, I will be using the following technology stack.  We will dive further into the details behind this in a later post, but I wanted to preview the stack so that potential readers can decide whether this series is right for them.

- MEAN Stack (MongoDB, ExpressJS, Angular 5+, NodeJS)
- Digital Ocean for hosting
- Sass / Bootstrap for CSS
- Gitlab for source code management
- Gitlab continuous delivery tool
- Terraform for automated infrastructure

## How to use this Series

This series is going to be LONG. It will take you weeks to finish if you follow it all the way through, and that is how it should be. We are building a production application here which is not an easy task.

For most people viewing this, you probably already have an idea where to go with various parts of the tutorial, so feel free to skip around to the sections you need help with. If you are taking this tutorial solely for learning purposes, I suggest going through each and every lesson!

Best of luck! If you have questions, feel free to leave them on the appropriate YouTube video.
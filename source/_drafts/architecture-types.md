---
title: Introduction to Software Architecture (Monolithic vs. Layered vs. Microservices)
date: 2019-04-24 05:21:18
tags: software architecture
---

<div class="series-introduction">If you are coming from part 4 of my series [How to Build a Production Web App](/blog/2019/build-production-web-app-part-3), you are probably wondering what my thought process was behind the architecture presented in the series.  This post should clear up any confusion and provide a quick introduction to the world of software architecture.
</div>

## Introduction

In this post, we will answer the following 3 questions: 

1. **Why do we need a software architecture?**
2. **What is a Monolithic Architecture?**
3. **What is a Layered Architecture?**
4. **What is a Microservices Architecture?**
5. **Other Architectures**

If you are a self-taught developer, new to the industry, or something of the sort, the concept of "software architecture" is intimidating.  Enterprise architects get paid lots of money because architecting a quality software is **difficult and requires experience**.

You may have the following concerns: 

* How am I supposed to architect a solution with little prior experience?
* There are so many architectures and design patterns.  What if I choose the wrong one?
* How can I create an entire architecture without knowing all the details about the code I'm going to write?

Throughout this post, we'll work through these concerns and figure out what this architecture thing is all about.

## Why do we need Software Architecture?

"Any intelligent fool can make things bigger, more complex, and more violent. It takes a touch of genius—and a lot of courage to move in the opposite direction"

From E.F. Schumacher's book _Small is Beautiful_, this quote embodies a lot of what architecting software means.  As a developer, it is always more fun to solve a complex problem in a complex way.  It's more fun to create a Rube Goldberg Machine than walking from point A to point B and dropping the marble in the cup.

Unfortunately, as a developer and architect, you get no additional bonus points for indirectly solving problems.  You make the real money by solving a complex problem in a simple way.

**Designing software architecture is about arranging components of a system to best fit the desired quality attributes of the system.**

* The user cares that your system is fast, reliable, and available
* The project manager cares that the system is delivered on time and on budget
* The CEO cares that the system contributes incremental value to his/her company
* The head of security cares that the system is protected from malicious attacks
* The application support team cares that the system is easy to understand and debug

There is no way to please everyone without sacrificing the quality of the system.  Therefore, when designing software architecture, you must decide _which quality attributes_ matter most for the given business problem.  Below are a few examples of quality attributes: 

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

Depending on what software you are building or improving, certain attributes may be more critical to success.  If you are a financial services company, the most important quality attribute for your system would probably be _security_ (a breach of security could cause your clients to lose millions of dollars) followed by _availability_ (your clients need to always have access to their assets).  If you are a gaming or video streaming company (i.e. Netflix), your first quality attribute is going to be _performance_ because if your games/movies freeze up all the time, nobody will play/watch them.

As you can see, the process of building software architecture is not about finding the best tools and the latest technologies.  It's about **delivering a system that works effectively**.  But why do we need an architecture to do this?

According to the book _[Software Architecture in Practice](https://amzn.to/2X1Uoaq)_, there are 13 reasons why Software Architecture is important for the success of a project.  Of those 13, I pulled out a few that might resonate with a smaller team or individual developer: 

**1. Architecture enables quality attributes** - For example, a peer to peer architecture is naturally going to have high _availability_ due to the decentralized nature of the system.  A microservices architecture will have high modifiability due to the segregation of duties.
**2. Architecture enables communication among stakeholders** - When the architecture closely resembles the structure of your company, everyone knows which part of the software they are responsible for.
**3. Architecture focuses on the assembly rather than creation of components** - Rather than focusing on how the code is written, architecture forces us to think about how the components in the system talk to one another.
**4. Architecture restricts design choices, enabling creativity in other areas** - By defining some boundaries in your software project, you know which areas you can get creative in without hurting the progress or quality of the system.

The book points out that design decisions made at the beginning of a project have a **disproportionate weighting** and restrict the ability to change certain areas of the software later on, so it is important to spend time to understand the requirements of the software and design it to the best of your ability from the start.

This is where an experienced architect has an advantage over a novice.  They can see much further into the future and anticipate how certain design decisions will impact the system.  

For those of us (including myself) not so experienced in designing architecture, we must accept that things won't be perfect and **design it anyway**.  Yes, revisions will have to be made, but what other choice is there?

There are [many architectures to choose from](https://en.wikipedia.org/wiki/Software_architecture#Architectural_styles_and_patterns), but not all of them are "beginner friendly" and sometimes require years of experience to implement correctly.  For example, creating an effective peer-to-peer architecture (i.e. Bitcoin, Bittorrent) is no easy undertaking.

To keep things simple, I will be walking through 3 common architectures that cover a wide variety of use cases.  Unless you have a _specific reason and design experience_ to justify going a different route, one of these three architectures will usually suffice no matter what technology stack you use.

I chose these three because they are natural progressions of each other.  The monolithic architecture is simpler than the layered architecture which is simpler than the microservices architecture.  There are other useful architectures like Event-Driven, Client-Server, Microkernel, and more, but if you do not understand the 3 below, it would not make sense to attempt any of these advanced architectures anyways.

I believe the easiest way to learn software architecture is to see it in practice.  You have certainly seen different architectures while reading through codebases, but you probably haven't recognized them.  My examples below are not meant to demonstrate the proper way to code an application, but rather to explicitly call out the various architectures that you can use within your codebase.  To demonstrate, I will be using NodeJS, ExpressJS, and MongoDB in the context of a web application.

## Monolithic Architecture

_All code mentioned below is stored in my [layered architecture repository on Github](https://github.com/zachgoll/monolithic-architecture-example-app)_

A monolithic architecture describes an application where everything is condensed into one codebase.  You may have different parts of the application split into folders, but changes to any part of the codebase will affect the other elements.

If you have ever taken tutorials online that teach you how to build a web application, you have most likely built a monolithic application.  It is by far the easiest to conceptualize starting out.  

A monolithic architecture describes an architecture where all of the following components are bunched into one codebase:

* Views (i.e. HTML, CSS, Javascript)
* Application/Business Logic (i.e. ExpressJS)
* Data Access/Database (i.e. MongoDB)

Although this architecture may seem ineffective, not all industry professionals believe it is useless.  For example, Martin Fowler [advocates for the use of monolithic architectures](https://martinfowler.com/bliki/MonolithFirst.html) when starting a new application.  He notes that those who start their applications as microservice architectures usually end up wasting time and energy because you don't start seeing the benefits of this architecture until the application becomes complex.

He suggests starting with a monolithic architecture and refactoring it later into a layered or microservice architecture when it becomes too large to handle all in one piece.

Let's take a look at the internals of a simple monolithic architecture.

### Application Structure

This simple monolithic application has a server, mock database, and home view.

```
database/
    mock-database.js
server/
    server.js
views/
    home.ejs
```

```javascript
// File: mock-database.js

function returnData() {

    const fakeData = {
        name: "Monolithic Architecture",
        author: "Zach Gollwitzer",
        blogPostUrl: "https://zachgoll.github.io/blog/2019/build-production-web-app-part-4" 
    };

    return fakeData;

}

module.exports = returnData;
```

```javascript
// File: server.js

var express = require('express');
var app = express();
var db = require('../database/mock-database');

// set the view engine to ejs
app.set('view engine', 'ejs');

// index page 
app.get('/', function(req, res) {
    const data = db();

    res.render('../views/home', {data: data} );
});

app.listen(8080);
console.log("Visit app at http://localhost:8080")
```

```html
<!-- File: home.ejs -->

<html>
    <head>
        <style>
            body {
                padding: 50px;
            }
        </style>
    </head>
    <body>
        <h1>Monolithic Architecture Application</h1>
        <hr>
        <p><strong>App Type: </strong><%= data.name %></p>
        <p><strong>Developer: </strong><%= data.author %></p>
        <p><strong>Blog Post: </strong><a href="<%= data.blogPostUrl %>">Build Production Web App Part 4 - Architecture</a></p>
    </body>
</html>
```

I know this application is rather simple (only 3 files!), but it has enough components to demonstrate the essence of a monolithic architecture.  The key concept to realize is that touching one part of this application will affect the rest of the application.

## Layered Architecture (also called "n-tiered")

_All code mentioned below is stored in my [layered architecture repository on Github](https://github.com/zachgoll/layered-architecture-example-app)_

After a while, your monolithic application will start getting big, you will start hiring people, and it will quickly become a mess.  Although many modern architects will turn to a microservices design to solve this problem (covered in the next section), another option to better segregate the duties of the application is to refactor your monolith into a layered architecture.

Unlike the monolithic architecture where everything is interconnected, the layered architecture splits your application into layers.  Usually, you will find the following layers (in order): 

1. Presentation Layer
2. Business Layer 
3. Data Access Layer

You may also stumble upon alternate terminology: 

1. Presentation Layer
2. Application Layer 
3. Domain Layer
4. Persistence Layer

No matter what you call the layers, the point is to create a "separation of concerns" where each layer is only allowed to use the layer directly below it.

In some cases, you may have a shared layer that has utility functions.  In this case, you could create an additional layer that is considered "open" for all layers to use.  Other layers are considered "closed" which means they can only use the layer below them.

<div class="clickable-images">{% asset_img layered-architecture-with-utils.png %}</div>

### Application Structure 

placeholder...

## Microservices Architecture

_All code mentioned below is stored in my [microservices architecture repository on Github](https://github.com/zachgoll/microservices-architecture-example)_

<div class="clickable-images">
{% asset_img microservices.png %}
</div>

### Application Structure

Placeholder...
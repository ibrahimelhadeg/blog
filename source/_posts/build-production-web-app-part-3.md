---
title: Build a Production Web App Part 3 - Design Document
date: 2019-04-07 19:08:45
tags:
---

Welcome to part 3 of the "Build a Production Web App" series.  If you are new here, I suggest starting from the beginning.

[Go To Part 1](https://zachgoll.github.io/blog/2019/build-production-web-app-part-1/)

In this part, we will be introducing the design document and explaining how it is created.

## Design Document

Alright, so the Statement of Work is complete, and we will assume it has been signed.  Now, the AGENCY and CLIENT will work together to produce a design document that will be the "guidebook" for the development of the project.

But what goes into a design document?

Unfortunately, there is no right answer to this question.  It would be nice if there was, but given all the possible variables introduced by each new project, it would be impossible to "templatize" the design document completely.

The goal of writing a design document is not to _fit a template_, but to _make your development phase easier_.  A design document could be scribbles on a whiteboard that you snap a photo of, a perfectly formatted Word Document, or a collection of loose papers that you should probably have stapled together!  Whatever the case, the design document is less of a _document_ and more of a _reference_.  As long as you have the required information, it works!

The design document will continually evolve as the project evolves and requirements change.  This is inevitable, but it doesn't mean that you scrap the effort altogether.  Get down on paper what you can because it will guide your development efforts.

### Slow and Steady Wins the Race

If you are starting a project from scratch, expect this section to be _painfully slow_.  You will be tempted to speed through this part, but having a complete design document will save your butt in a big way down the road.

Read the above section once more.

Luckily, the software we are building in this series is nothing compared to that of large corporations.  We are building from scratch which means we do not have to worry about integrating into existing systems, meeting a diverse set of stakeholder business requirements, or worrying about funding to continue development.

### How to Read this Document

Below is the completed first version of the design document.  As a quick spoiler alert, I'll remind you that this document will go through many more iterations, but the state you see below is my first stab at it.

I suggest you read through the entire thing right now to get an idea what is in it, and then move on to the next few posts in the series.  When you have completed the posts on architecture, database design, and API design, come back and read it once more to bring things full circle.

<div class="document">
<span style="font-size: 1.2rem;">**Design Document**</span>

**Version: 1.0**
**Date: 03/24/19**
<div></div>

### Contents Summary

The contents of the design document is largely made up of the deliverables in the "Design Phase" of the SoW.  Just like stated in the SoW, the design document will expand in size to accomodate new versions of the application as they are released.  The design document below represents v1.0 of the application.  Subsequent versions and new features will require amendments to the SoW, additional funding, etc.

<div class="sow-comment">This is a comment.  You will see these throughout the design document, but please note these comments _are not part of the design document_.  They are here for explanation only, and will include links to the extended descriptions of how each section was created.</div>

[User Stories and Wireframes](#User-Stories-and-Wireframes)
[Brand Style Sheet](#Branding-Guide)
[Architectural Design](#Architectural-Design)
[Data Design](#Data-Design)

### User Stories and Wireframes

<div class="sow-comment">For a longer explanation of how these wireframes were created, go to the [wireframes extended description](#Wireframes-Extended-Description).
</div>

The wireframes below are for v1.0 of the application.  Additional features will be added via a change request, amendment to the SoW, and an additional funding request (if applicable).

{% asset_img home_page.png %}

{% asset_img score_entry.png %}

{% asset_img video_upload.png %}

{% asset_img analytics_dashboard.png %}

{% asset_img profile_and_settings.png %}

{% asset_img link_ghin_account.png %}

<div class="sow-comment">If the graphic design of your application is highly important to you, you may hire someone to create "mockups" based on the wireframes.  These can be created in Photoshop, Illustrator, or another mockup tool and should represent the actual end state of the application.  This includes correct colors, logos, animations, etc.  For this project, I am not concerned with creating a stunning visual experience.  I intend to make the app look **clean and minimal**, but not overly creative.</div>

### Branding Guide

<div class="sow-comment">For an explanation on how the document below was created, go to the [Style Guide Extended Description Section](#Style-Guide-Extended-Description).  For a PDF copy of the style guide shown below, {% asset_link prod-web-app-style-guide.pdf "click here" %}.
</div>

{% asset_img prod-web-app-style-guide.png %}

### Architectural Design

<div class="sow-comment">For an explanation on how the section below was created, continue to [part 4 of the series]().
</div>

### Data Design

<div class="sow-comment">For an explanation on how the section below was created, continue to [part 5 of the series]().
</div>

### API Design

<div class="sow-comment">For an explanation on how the section below was created, continue to [part 6 of the series]().
</div>

</div>

## Wireframes Extended Description

These wireframes do not need to be as neat as I have made them.  You can simply draw them out on sheets of printer paper and throw those into the design document (assuming your handwriting is not atrocious).  Click the link below to see my initial wireframes on paper.  I always start with a pen and paper as it helps me visualize and focus!

<span style="font-size: 1.0rem; color: #00b7c8 !important;">{% asset_link prod-web-app-wireframes.pdf "PDF Link: Wireframes Rough Draft on Paper" %}</span>

The wireframes in the design document took me about 2-3 hours to fully complete (although I had done market research, brainstorming, etc. beforehand).  Many of the wireframe ideas were inspired by the survey that I gave to my golf site's email list.  You can see the survey results below (long form responses are abbreviated):

<span style="font-size: 1.0rem; color: #00b7c8 !important;">{% asset_link survey-results.pdf "Market Research Survey" %}</span>

I created wireframes using [The Pencil Project](http://pencil.evolus.vn/Downloads.html).  This tool is completely free forever and is available on all operating systems.  There are **plenty** of paid options (including the Adobe product suite), but unless you are a full time designer, they are rarely worth the expensive monthly subscriptions.  I absolutely loved my experience using Pencil, and I think you will too!  {% asset_link prod-web-app-wireframes.epgz "Here is the source file" %} for my wireframes that you could use as a template.  You can only open this file in the Pencil program itself.

## Style Guide Extended Description

For an application like the one we are building here, the branding style guide does not need to be as complex as you would see for a company website.  Nevertheless, we still need to create one to guide front-end development.  This is primarily to avoid that moment when you login to an application and see four shades of red on one page (cringe).

Within a style guide, I consider three main sections: 

1. **Colors** - staying consistent with your colors will save you headaches down the road.  Decide early which color will be used for headings, main text, hyperlinks, buttons, etc.
2. **Fonts** - I almost always use Google Fonts.  Google Fonts not only has hundreds of free fonts; they also provide recommendations of which fonts pair well together.  Although some fonts might sound like a good idea, it is usually good to go with a serif or sans serif.
3. **Logo Variations** - I recommend having two logos.  The large logo will be the main one, and the small logo will be used for social profiles and other small spaces.  You will also need light and dark variations so that your logo can be put on both dark and light backgrounds.

Since I will be using Bootstrap CSS framework for this app, the style guide will be simple using two tools: 

1. [Bootswatch](https://bootswatch.com/flatly/) - A variety of custom Bootstrap color themes ready out of the box
2. [Canva](https://www.canva.com/) - Creating the Style Guide Document and maybe even the logo as I did!

Of course you can get far more creative with this section of the design document.  You could even hire an entire design department.  If you are a startup with funding, this might be a route to take.  In my case, I don't care to spend money nor does the application I am making warrant such an elaborate design process.

Next part in series: [Building the Architecture]()
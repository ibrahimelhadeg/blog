---
title: Build a Production Web App Part 2 - Statement of Work
date: 2019-03-15 14:26:06
tags:
---

<div class="series-introduction">Welcome to part 2 of the "Build a Production Web App" series.  If you are new here, I suggest starting from the beginning.

[Go To Part 1](/blog/2019/build-production-web-app-part-1/)
</div>

<link href="https://fonts.googleapis.com/css?family=Tangerine" rel="stylesheet">

## What First?

As a developer or startup, it is tempting to jump into the deep end of the pool and start building your great idea.  I've caved into that temptation many times, and it rarely ends well.  Sure, you might be able to build a nice looking prototype, but that is not what we are aiming to do here.  We want a clean application that can be supported long into the future.

To achieve this, we need to start by creating a project plan.

## Teams Involved in this 

Given the fact that we will be developing the app start to finish without hiring any contractors, it begs the question whether a project plan is even necessary.  Why plan when you can just get started and figure it out along the way?

Although there is no funding and no contractors to hire for this project, we still need a project plan.  Not only will it help simulate what a real project might look like, but it will also allow us to narrow the scope of the code we have to write.

## Choosing a Project Management Method

There are 1,000 correct ways to plan and manage a project, but you will generally see two standard approaches in the software industry:

1. Waterfall Approach
2. Agile Approach (continuous delivery)

At the time of writing, the waterfall approach carries a negative stigma in PM (project manager) circles because can you really get the planning and budget right for a 3 year project anyways?  What's the point of a waterfall approach if the project changes so much along the way that the original plan is deemed useless?

I won't argue that the waterfall approach is better because in terms of efficiency, it's usually not.  That said, when working on a project for a client, that client is going to want to know a rough estimate of how long the project will take and for what cost.  You cannot just walk up to a client and say "We don't know how much this will cost, and we don't know how long it will take.  But we'll try our best?".

Likewise, it would be hard for you as the reader/viewer of this series if I had no structure behind the project.  For that reason, we are going to combine the best of both worlds and use both Agile methods and Waterfall methods.

As a programmer, I love being able to make binary choices.  It's literally at the core of what I do.  But sometimes, blending the best of both worlds is the most reasonable approach.  

You will see this paradigm throughout this tutorial many times.  Not only will we be using Agile and Waterfall planning methods together, but we will also be mixing object oriented programming with functional programming; Promises with Observables.  The list goes on.

Although we are starting this project from the ground up, you will not always have the freedom to design the entire system from scratch.  Many times, you will have to build a new system while integrating it into legacy software architectures.  It feels comfortable to choose one or the other, but in programming, you have to work with what you've got.

## A Note on Cost Estimates

I have already come up with the idea to build, but in many cases, the ideation phase can take weeks depending on the size of the project.

A project like this could cost around $20K-$30K depending on some of the features you are looking for (based on a price estimate from [Estimate My App](https://estimatemyapp.com/)).  Since I am developing it for myself, there is no concept of a client, and no cost estimates to make.  Nevertheless, we will go through some of the considerations I would make had I decided to hire a team to build this.

**Most of the costs are personnel**.  The software itself does not cost much.  As you will see, you can build a production web app with almost 100% open source (free) software.  The only _non-personnel_ cost we will incur across this project will be hosting costs, domain name costs, and a few other miscellaneous expenses.  These costs will continue perpetually into the future, so be sure that you have the funding to support your new application.  

To grasp just how expensive the labor for a software project can get, consider a project that will take 10 weeks.  If you have a team of **5** developers working at **$65 per hour** (that's even on the low end) every day of the week, you'll end up paying around **$130K** for the project.

10 weeks x 40 hours / week x 5 contractors = 2,000 hours

2,000 hours x $65 per hour = $130,000 total cost

Didn't someone say open source software was completely free?...

In reality, cost estimates are more complex and involve multiple meetings with the client to discover what their product needs are, what their budget is, how fast they need the project completed, and other factors.  Through the course of the project, the client could also request additional features, which will result in a request for funding.  If the client is not knowledgeable about software in general, the cost estimation process can turn out to be quite difficult.  Since our goal is to _write_ software and not _manage a project_, there is no need to explore this topic in depth.

A cost estimate is helpful, but in order to start the project, we need a Statement of Work (SoW) and approval of funding.  The SoW can be as simple or as complex as you want it to be.  The ultimate goal with an SoW is to _protect yourself_ as a contractor against the client and define the work to be done.  As a software company offering your development services, if there are any items that may become points of contention in the future, **put them in the SoW**.

## Proposal and Statement of Work

_Before starting, I will give credit where credit is due and thank Ben from [The Digital Project Manager](https://thedigitalprojectmanager.com/) for the SoW template (and tons of other advice on project management!).  If you would like the full template, please hop over to Ben's site for a free download._

**An SoW should be as short and concise as possible while protecting the contractor from unfair business practice**

In other words, the SoW is a **contractor's legal protection** against an unfair client.  In an ideal situation, the client will respect the contractor's time and resources and not demand extra work for an unfair cost.  That said, you may find yourself with a client who wants "just one more feature" for free.

Below is a simple SoW that I created for this project.  Please note that this SoW would only be suitable for small development projects.  For larger projects, you may need additional information filled out for the sections relating to project governance, copyright guarantees, intellectual property definitions, point of contact information, contingency resolution, change request processes, financial budgets, and more.

<div class="document">

<span style="font-size: 1.2rem;">**Statement of Work**</span>

**Version: 1.0**
**Date: 03/18/19**

<h3>Contents Summary</h3>
This document has 2 distinct parts. The first section outlines over-arching project information; the second part defines the detail of each phase and will be extended through the course of the project as each phase ends.

<div class="sow-comment">This is a comment.  You will see these throughout the SoW, but please note these comments _are not part of the SoW_.  They are here for explanation only.</div>

**Part 1**

[Project Summary](#Project-Summary)
[Project Process](#Project-Process)
[Project Budget](#Project-Budget)
[Project Milestones](#Project-Milestones)
[Project Governance](#Project-Governance)
[Project Terms and Conditions](#Terms-and-Conditions)

**Part 2**

[Phase 1: Ideation and Definition](#Phase-1-Ideation-and-Definition)
[Phase 2: Development / Delivery](#Phase-2-Development-Delivery)
[Phase 2: Review and Support](#Phase-3-Review-and-Support)

[Appendix](#Appendix)

### Project Summary

Project Number:                 1
Project Name:                   Build Production Web App YouTube Series
Start date:                     3/21/19
Deadline:                       TBD
Project Labor Budget:           $0 for phase 1
Project Non-Labor Budget:       $0 for phase 1
Delivery Approach:              Waterfall/Agile Hybrid
Business Drivers:               Website traffic volume, Returning visitor count
Project Owner:                  Zach Gollwitzer

<div class="sow-comment">For a larger project, this is where you would define the stakeholders involved (and there contact details).  This could include the project owner, project manager, idea owner, finance owner, testing engineer, security engineer, lead architect, etc.</div>

**Risks**

* Server downtime caused by increased site traffic
* Lack of project governance (no segregation of duties)
* Integration with external APIs not working correctly 

**Project Overview**

The project has two goals: 

1. To demonstrate the process and internals of building a production-ready web application
2. To drive more users to www.thediygolfer.com through a value-added statistics tracking and practice application.

The focus will be primarily on the technical development of the project rather than components like legal issues, project management, project governance, graphic design, etc.  The secondary goal is to achieve selected business objectives.

**Expected Outcomes**

The expected outcomes of the project are: 

1. Educate a large technical audience the basics of building a production web application using the MEAN stack (MongoDB, Express, Angular, NodeJS)
2. Gain 1000 new users within 1 year of first production release

### Project Process

**Delivery Approach**

Although I work closely to the project timeline, budget, and other requirements, the nature of software development is oftentimes volatile and timelines, budgets, or requirements may need adjustment at various points.

To minimize my risk as contractor, I break out the phases of the project into three distinct phases.  The 3 phases are: Ideation and Definition, Development, and Review.  Below are summaries of each.

**Phase 1: Ideation and Definition**

All projects begin in phase 1 with the SoW (this document) to finalize budgets, requirements, and other outstanding concerns between the AGENCY and CLIENT.  In many cases, Phase 1 starts at the first interaction between AGENCY and CLIENT.

<div class="sow-comment">Obviously, I am both the AGENCY and the CLIENT in this situation, but this syntax is common in SoWs.</div>

When the SoW is signed, the project will commence with a kick off meeting between the AGENCY and CLIENT.  In this meeting, stakeholders from both parties will discuss the problem at hand and map out a plan to successfully complete the design document from which the project will follow.  To complete this document, the stakeholders may require things like competitive analysis, brand exploration, stakeholder interviews, demographic research, etc.  The design document includes a portion of or all of the following.

* Overall Business Objective
* Data Design
* Architectural Design
* UI/UX Design (wireframes, mockups)
* Branding Style Guide
* Content Strategy and/or creation
* Sitemap
* Copywriting

**Phase 2: Development / Delivery**

Phase 2 is generally the longest phase and is where AGENCY builds out the solution architected in Phase 1.  The steps are listed for this phase: 

1. Back-end development
2. Front-end development
3. Quality Assurance testing
4. User Acceptance Testing
5. Final Testing
6. Deployment

**Phase 3: Review and Support**

The final phase necessary to transition between delivery and ongoing support.  A web application requires hosting, domain name renewal, and other support services.  When the project is complete, AGENCY will transfer all support costs to CLIENT.  AGENCY will also provide 2 months of ongoing support which may include troubleshooting application issues or training internal personnel how to support the application.  When the 2 months is up, the AGENCY will require additional budget for further support.

<div class="sow-comment">In a real-life scenario, the AGENCY may specify an hourly support rate.</div>

### Project Budget

<div class="sow-comment">Yes, I do have hosting costs and domain name costs, but they are so negligible that I will not include them.  In this section, you would generally see a budget broken out over X number of years for both _labor_ and _non-labor_ project expenses.</div>

### Project Milestones

<div class="sow-comment">The below milestones are very characteristic of a Waterfall project planning method.  Many Agile advocates argue that planning this far in advance is pointless, but I wanted to include it here so that you as the reader have an idea where this project is going.
</div>

<div id="before-table"></div>

| Milestone Description                 |      Date      |
|---------------------------------------|:--------------:|
| v1.0 Planning Complete                |     4/5/19     |
| v1.0 Back-End Development Complete    |     4/30/19    |
| v1.0 Front-End Development Complete   |     6/15/19    |
| v1.0 QA Testing Complete              |     6/30/19    |
| v1.0 User Acceptance Testing Complete |     7/10/19    |
| v1.0 Final Testing and Deployment     |     8/31/19    |
| v1.1 Release (small update to demonstrate how to push updates) | 9/5/19 |
| v1.1 Mobile App (IOS) release         |     9/30/19    |
| v1.2 Release (web and mobile update)  |     10/5/19    |
| Future Releases                          | Not part of series |

### Project Governance

<div class="sow-comment">As you would have guessed, there is not much governance on this project.  Usually, this section would contain the names of people responsible for initiating/approving various stages in the development lifecycle.  This might include the names of people on the working group, steering committee, finance committee, etc.  It also would include procedures for things like change management (how do we fund a change?), escalation (what if something goes wrong?), and reporting (what weekly reports do we need to stay updated on the status of this project?  And who looks at them?).</div>

### Terms and Conditions

<div class="sow-comment">Everything above this section is at the AGENCY's discretion, but this section is pretty standard.  Many SoWs have a similar "Terms and Conditions" section because it is _legal protection_ for you as the AGENCY.  In my case, I have removed some of the clauses due to the fact that I am both AGENCY and CLIENT, but there are tons of these legal sections posted online that you can copy from if you need a full one.</div>

**Master contract**

Work undertaken in this project will be in line with the terms defined in the master contract.

<div class="sow-comment">In many cases, an AGENCY will have a relationship with the CLIENT and win more than one development project.  In these situations, an MSA (master service agreement) will govern the _relationship_ between AGENCY and CLIENT while the SoW will manage the specific _work to be done on each project_.</div>

**Statement of Confidentiality**

The information contained in this document is proprietary to AGENCY and is privileged and confidential. AGENCY submits this document to the client with the understanding that it will be held in the strictest of confidence of the client, its employees, associates, agents and assignees. This document will not be disclosed, duplicated in any manner, or used, in whole or in part, for any purpose other than the sole evaluation of AGENCY’s qualifications for the proposed project. Any disclosure, duplication, or use, in whole or in part of this document, may only be obtained with the prior written consent of AGENCY.

General assumptions: 

- If a delay occurs in obtaining a sign-off due to delays caused by the client team and not due to non-performance by AGENCY, there may be an increase in costs and/or extension to the timeline, for which the client is responsible.
- The client’s final written acceptance is due within 3 days of each deliverable submission; further delays may result in additional costs and delayed timescales. These terms are applicable for AGENCY for this project though not stipulated in the overall agency contract.  This will be built into the project plan.  The number of reviews may decrease to keep on schedule but will be collectively determined between the Project Manager and Client Acceptor.
- Two rounds of revisions for each deliverable have been included where appropriate and possible as specified within the project plan. If the client exceeds the defined number of revisions for a specific deliverable, the project scope will be exceeded and the project timeline and budget may be affected.
- The costs included have been based on the information supplied by the client and the stated deliverables. All outside expenses, including, but not limited to travel and lodging per day, shipping, supplies and rental equipment are not included in the Fixed Price and will be billed to the client in addition to the fixed price on a monthly basis in arrears.
- If the scope of the project changes from the specifications agreed or AGENCY is required to provide additional services not described in the project plan (i.e. additional reviews), such changes will be documented in a Change Request and may impact timing and costs. The Change request will require sign-off by the Project Acceptor before work described therein can commence. AGENCY will invoice the client for the total change request cost.
- The project depends on the close involvement of the client’s internal teams to provide input, and to review and approve deliverables in-progress, and to be available for presentations and conference calls throughout the engagement. The client will also be responsible for obtaining the necessary involvement of additional business stakeholders as appropriate and collating their feedback.
- All software, documentation and other materials comprised in the Deliverables which were in existence prior to the date of this estimate, together with any device, programming, documentation, media or other materials used as a programming tool by AGENCY in the development of the Deliverables, proprietary to AGENCY and not designed primarily for use or primarily used in conjunction with the Deliverables together with all intellectual property rights in such “Pre-Existing Materials”, shall remain the property of AGENCY.
- AGENCY shall make the client aware of any software, documentation or other materials, the intellectual property rights in which are owned by a Third Party (“Third Party Materials”) together with the terms of use applicable to such Third Party Materials.
- The client will be responsible for providing AGENCY with pre-approved electronic files of all logos, product images, written content, and subject matter experts as required. The client will be responsible for ensuring that they have all necessary rights and licenses to such assets.
- The client will be responsible for factual accuracy and legal approval of all content. Textual content will be grammar/spell checked and approved by the client before being integrated into any Deliverable.
- The client will be responsible for management and coordination with AGENCY and all internal client Stakeholders for this project.
- This statement of work is limited to hours available within the total project estimate; contingency only utilized with prior approval.
- Project scope is based on the described activities – deliverables are assumed to be a representation of the recommended approach. Should the approach change, or additional currently non-specified requirements be added, change requests may be issued.
- Content, imagery and links will be supplied by before AGENCY will begin work on the task
- This estimate is priced in US dollars.
- These costs have been prepared exclusive of tax

<div class="sow-comment">Yes, all of this is boring, but it is your only protection as the AGENCY from a bad client!</div>

### Phase 1: Ideation and Definition

<div class="sow-comment">The remaining parts of the SoW define the actual work to be done.</div>

A design document will be created to guide the development phase.  The design document will include:

- User Stories and Wireframes will be completed for all pages in the v1.0 release.  For each page, a summary of the page will be provided, a wireframe, and acceptance criteria will be drafted.  The v1.0 release will include all pages developed in this deliverable and no more.
- Brand Style Sheet will include a color wheel, logo, font style, buttons, tables, etc. 
- The data design will support the user stories and wireframes.  The API will be created to provide the front-end access to our database.
- The architectural design will show how our front-end, back-end, and automated infrastructure fits into a cohesive, production application.
- The design document will bring all these pieces (and others) together.  This document will be referenced for all development.

<div id="before-table"></div>

| Milestone Description       |   Date  |
|-----------------------------|:-------:|
| User Stories and Wireframes | 3/21/19 |
| Brand Style Sheet           | 3/22/19 |
| Data and API Design         | 3/29/19 |
| Architectural Design        |  4/2/19 |
| Design Document Finalized   |  4/5/19 |


<div class="sow-comment">For funded projects, this section would also include hours and costs of resources decked to each deliverable.</div>

**Approval**

This section is to verify the acceptance of the work stated above.

AGENCY Signature: <span class="signature">Zach Gollwitzer</span>
CLIENT Signature: <span class="signature">Zach Gollwitzer</span>

<div class="sow-comment">Since this SoW is an iterative document where each phase is approved after the previous is completed, there should be an approvals section where AGENCY and CLIENT agree to the deliverables listed above and the funding listed above (if there is any).</div>

### Phase 2: Development / Delivery

This phase has not been approved at the time of writing and will be updated at a future date.

### Phase 3: Review and Support

This phase has not been approved at the time of writing and will be updated at a future date.

### Appendix

<div class="sow-comment">This section is for anything that would be too detailed for the main part of the SoW.  You may want to write an extended description for each deliverable in a given phase, add some definitions of terminology, etc.</div>

</div>

<br>
<hr>
[Next Post in Series: Design Document](/blog/2019/build-production-web-app-part-3/)

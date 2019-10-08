---
title: MEAN Stack Authentication with Okta and JWT
tags:
---

As a new(ish) developer, one of the things that took longer than most concepts for me to grasp was the topic of authentication with Single Page Applications (SPAs).

With the MEAN stack, you have an Angular front-end which has options for protecting routes via some sort of ID token and access token (OpenID spec on top of Auth2.0), and you have an Express backend where you can use those same tokens to authenticate users to your API (routes).

Starting out, I wanted so badly to contain my authentication to one or the other.  Why would I build Angular route protection when I could just do it with the Express backend and vice-versa?

For example, let's say I have a route in my application called `https://localhost:8080/#/protected-content`, and a homepage route `https://localhost:8080/#/`.  With the homepage route, I want anybody in the world with an internet connection to be able to see it.  On the contrary, the protected route should only be visible to my logged in users.

We will say that when a user is logged in, the database loads a super secret treasure map to this protected route's page.  When I visit `https://localhost:8080/#/protected-content` in the browser, the Angular router authenticates and authorizes me to view this treasure map. 

But this treasure map is stored on my Express server, and can only be retrieved by performing a GET request to `https://localhost:8080/get-treasure-map`.

So in this scenario, when a user visits `https://localhost:8080/#/protected-content`, my Angular application has to do the following: 

1. Request the treasure map from `https://localhost:8080/get-treasure-map` (passing my ID and Authorization tokens in the HTTP request header)
2. Check with the Angular router whether there is a user logged in
3. Check whether the logged in user has authorization to view this page
4. Load the page

Seems fine right?

Here's where things get tricky.  What if my backend Express API denies authorization to the current user at the API endpoint `https://localhost:8080/get-treasure-map`? 

Here, we have a scenario where the Angular application and the Express application are in disagreement.

* The Angular application knows that there is a logged in user and is allowing that user to view the super secret page with the treasure map.
* The Express application knows that there is a logged in user, but based on that user's ID and Authorization tokens, is denying access to the `/get-treasure-map` route.

Because of this, when we load the `/#/protected-content` on the front end, there will appear to be a page loaded, but the secret treasure map will not appear on the page.

Here, we have implemented two completely separate authentication schemes on the backend and the front end.

For the longest time, this was a conflicting thought to me, but there are actually valid uses for such a scheme.

What if you had an application where users are of different pay grades?  For example, Vimeo is a service where depending on your subscription status, you get access to progressively more features.  This would be a case where the front-end may give access to all routes, but only allow access to a certain scope of protected resources and features.


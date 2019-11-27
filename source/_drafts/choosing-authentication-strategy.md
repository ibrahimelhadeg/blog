---
title: A Simple Authentication Strategy for Small Organizations (NodeJS, Express, Angular)
tags:
    - Node authentication
    - JWT
    - OAuth
    - OpenID
---

In this post, I am going to walk through why the `Passport-JWT` authentication strategy is a simple, reliable solution for small teams and startups implementing a Node/Express + Angular web app. 

In addition, I will be walking through the following topics: 

* What authentication options are available (and how they work)
* How to implement a Passport JWT strategy within a Node/Express + Angular application (database agnostic)

If you are just looking for an implementation tutorial, [skip to this section](#implementation).

## Authentication Choices

{% asset_img auth-types.PNG %}

Above is a high level overview of the main authentication choices available to developers today.  This tutorial could become a full length book if I explored each in full detail, so my aim is to uncover the reason for each in the development ecosystem.

### Session Based Authentication

If we were to create a lineage for these authentication methods, session based authentication would be the oldest of them all, but certainly not obsolete.  This method of authentication is "server-side", which means our Express application and database work together to keep the current authentication status of each user that visits our application.

To understand the basic tenets of session-based-authentication, you need to understand a few concepts: 

* Basic HTTP Header Protocol
* What a cookie is
* What a session is
* How the session (server) and cookie (browser) interact to authenticate a user

There are many ways to make an HTTP request in a browser.  An HTTP client could be a web application, IoT device, command line (curl), or a multitude of others.  Each of these clients connect to the internet and make HTTP requests which either fetch data (GET), or modify data (POST, PUT, DELETE, etc.).

For explanation purposes, let's assume that: 

Server = www.google.com
Client = random guy in a coffee shop working on a laptop

When that random person from the coffee shop types `www.google.com` into their Google Chrome browser, this request will be sent with "HTTP Headers".  These HTTP Headers are key:value pairs that provide additional data to the browser to help complete the request.  This request will have two types of headers:

1. General Headers
2. Request Headers

To make this interactive, open Google Chrome, open your developer tools (right click, "Inspect"), and click on the "Network" tab.  Now, type `www.google.com` into your address bar, and watch as the Network tab loads several resources from the server.  You should see several columns like Name, Status, Type, Initiator, Size, Time, and Waterfall.  Find the request that has "document" as the "Type" value and click on it.  You should see all the headers for this request and response interaction.

The request that you (as the client) made will have General and Request headers resembling (but not exact) the following: 

```
General Headers
  Request URL: https://www.google.com/
  Request Method: GET
  Status Code: 200

Request Headers
  Accept: text/html
  Accept-Language: en-US
  Connection: keep-alive
```

When you typed `www.google.com` into your address bar and pressed enter, your HTTP request was sent with these headers (and probably a few others).  Although these headers are relatively self explanatory, I want to walk through a few to get a better idea of what HTTP Headers are used for.  Feel free to look up any you don't know on [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers).

The `General` headers can be a mix of both request and response data.  Clearly, the `Request URL` and `Request Method` are part of the request object and they tell the Google Chrome browser where to route your request.  The `Status Code` is clearly part of the response because it indicates that your GET request was successfull and the webpage at `www.google.com` loaded okay.

The `Request Headers` only contain headers included with the request object itself.  You can think of request headers as "instructions for the server".  In this case, my request tells the Google server the following: 

* Hey Google Server, please send me HTML or text data.  I'm either incapable or not interested in reading anything else right now!
* Hey Google Server, please only send me English words
* Hey Google Server, please don't close my connection with you after the request is over

There are many more request headers that you can set, but these are just a few common ones that you will probably see on all HTTP requests.

So when you searched for `www.google.com`, you sent your request and the headers to the Google Server (for simplicity, we will just assume it is one big server).  The Google Server accepted your request, read through the "instructions" (headers), and created a _response_.  The response was comprised of: 

* HTML data (what you see in your Browser)
* HTTP Headers

As you might have guessed, the "Response Headers" were those set by the Google Server.  Here are a few that you might see: 

```
Response Headers
  Content-Length: 41485
  Content-Type: text/html; charset=UTF-8
  Set-Cookie: made_up_cookie_name=some value; expires=Thu, 28-Dec-2020 20:44:50 GMT;
```

These response headers are fairly straightforward with the exception of the `Set-Cookie` header.

I included the `Set-Cookie` header because it is exactly what we need to understand in order to learn what Session based Authentication is all about (and will help us understand other auth methods later in this post).

#### How Cookies Work

Without Cookies in the browser, we have a problem.

If we have a protected webpage that we want our users to login to access, without cookies, those users would have to login every time they refresh the page!  That is because the HTTP protocol is by default "stateless".

Cookies introduce the concept of "persistent state" and allow the browser to "remember" something that the server told it previously.

The Google Server can tell my Google Chrome Browser to give me access to a protected page, but the second I refresh the page, my browser will "forget" this and make me authenticate again.

This is where Cookies come in, and explains what the `Set-Cookie` header is aiming to do.  In the above request where we typed in `www.google.com` into our browser and pressed enter, our client sent a request with some headers, and the Google Server responsed with a response and some headers.  One of these response headers was `Set-Cookie: made_up_cookie_name=some value; expires=Thu, 28-Dec-2020 20:44:50 GMT;`.  Here's how this interaction works: 

Server: "Hey client!  I want you to set a cookie called `made_up_cookie_name` and set it equal to `some value`.

Client: "Hey server, I will set this on the `Cookie` header of all my requests to this domain until Dec 28, 2020!"

We can verify that this actually happened in Google Chrome Developer Tools.  Go to "Application"->"Storage" and click "Cookies".  Now click on the site that you are currently visiting and you will see all the cookies that have been set for this site.  In our made-up example, you might see something like: 

| Name                | Value      | Expires / Max-Age        |
|---------------------|------------|--------------------------|
| made_up_cookie_name | some value | 2020-12-28T20:44:50.674Z |

This cookie will now be set to the `Cookie` **Request Header** on all requests made to `www.google.com` until the expiry date set on the cookie.

As you might conclude, this could be extremely useful for authentication if we set some sort of "auth" cookie.  An overly simplified process of how this might work would be: 

1. Random person from the coffee shop types `www.example-site.com/login/` into the browser
2. Random person from the coffee shop fills out a form on this page with a username and password
3. Random person's Google Chrome Browser submits a POST request with the login data (username, password) to the server running `www.example-site.com`.
4. The server running `www.example-site.com` receives the login info, checks the database for that login info, validates the login info, and if successful, creates a response that has the header `Set-Cookie: user_is_authenticated=true; expires=Thu, 1-Jan-2020 20:00:00 GMT`.
5. The random person's Google Chrome browser receives this response and sets a browser cookie:

| Name                | Value      | Expires / Max-Age        |
|---------------------|------------|--------------------------|
| user_is_authenticated | true | 2020-12-28T20:44:50.674Z |

6. The random person now visits `www.example-site.com/protected-route/`
7. The random person's browser creates an HTTP request with the header `Cookie: user_is_authenticated=true; expires=Thu, 1-Jan-2020 20:00:00 GMT` attached to the request.
8. The server receives this request, sees that there is a cookie on the request, "remembers" that it had authenticated this user just a few seconds ago, and allows the user to visit the page.

#### The Reality of this Situation

Obviously, what I have just described would be a highly insecure way to authenticate a user.  In reality, the server would create some sort of hash from the password the user provided, and validate that hash with some crypto library on the server.

That said, the high-level concept is valid, and it allows us to understand the value of cookies when talking about authentication.

Keep this example in mind as we move through the remainder of this post.

#### Sessions

Sessions and cookies are actually quite similar, and can get confused.  The _main difference_ between the two is the **location** of variable storage.

In other words, a Cookie is _set_ by the server, but stored in the Browser.  If the server wants to use this Cookie to store data about a user's "state", it would have to come up with an elaborate scheme to constantly keep track of what the cookie in the browser looks like.  It might go something like this: 

* Server: Hey browser, I just authenticated this user, so you should store a cookie to remind me (`Set-Cookie: user_auth=true; expires=Thu, 1-Jan-2020 20:00:00 GMT`) next time you request something from me
* Browser: Thanks, server!  I will attach this cookie to my `Cookie` request header
* Browser: Hey server, can I see the contents at `www.domain.com/protected`?  Here is the cookie you sent me on the last request.
* Server: Sure, I can do that.  Here is the page data.  I have also included another `Set-Cookie` header (`Set-Cookie: marketing_page_visit_count=1; user_ip=192.1.234.21`) because the company who owns me likes to track how many people have visited this specific page and from which computer for marketing purposes.
* Browser: Okay, I'll add that cookie to my `Cookie` request header
* Browser: Hey Server, can you send me the contents at `www.domain.com/protected/special-offer`?  Here are all the cookies that you have set on me so far. (`Cookie: user_auth=true; expires=Thu, 1-Jan-2020 20:00:00 GMT; marketing_page_visit_count=1; user_ip=192.1.234.21`)

As you can see, the more pages the browser visits, the more cookies the Server sets, and the more cookies the Browser must attach in each request Header.

The Server might have some function that parses through all the cookies attached to a request and perform certain actions based on the presence or absence of a specific cookie.  To me, this naturally begs the question... Why doesn't the server just keep a record of this information in a database and use a single "session ID" to identify events that a user is taking?

This is exactly what a session is for.  As I mentioned, the main difference between a cookie and a session is _where_ they are stored.  A session is stored in some Data Store (fancy term for a database) while a Cookie is stored in the Browser.  Since the session is stored on the server, it can store sensitive information.  Storing sensitive information in a cookie would be highly insecure.

Now where this all gets a bit confusing is when we talk about using cookies and session _together_.

Since Cookies are the method in which the client and server communicate metadata (among other HTTP Headers), a session must still utilize cookies.  The easiest way to see this interaction is by actually building out a simple authentication application in Node + Express + MongoDB.  I will assume that you have a basic understanding of building apps in Express, but I will try to explain each piece as we go.

Setup a basic app: 

```
mkdir session-auth-app
cd session-auth-app
npm init -y
npm install --save express mongoose dotenv connect-mongo express-session
```

Here is `app.js`

```javascript
```


https://developer.mozilla.org/en-US/docs/Web/HTTP/Session
https://stackoverflow.com/questions/11142882/what-are-cookies-and-sessions-and-how-do-they-relate-to-each-other
https://hueniverse.com/introducing-oauth-2-0-b5681da60ce2
http://www.passportjs.org/packages/passport-jwt/
http://www.passportjs.org/packages/passport-auth0/

## Implementation


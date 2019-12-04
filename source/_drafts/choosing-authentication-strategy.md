---
title: A Simple Authentication Strategy for Small Organizations (NodeJS, Express, Angular)
tags:
    - Session Based Authentication
    - JWT Authentication
---

In this post, I am going to walk through why the `Passport-JWT` authentication strategy is a simple, secure solution for small teams and startups implementing a Node/Express + Angular web app. 

To understand why a JWT authentication flow is the best choice for this situation, I am going to take you through what authentication options are available to you, how they work, and how to implement them (with the exclusion of OAuth as this is out of scope).

Since this post is long and detailed, if you are already familiar with a topic discussed, just skip it.  Likewise, if you are just looking for instructions on how to implemement a particular authentication method, you can jump to those sections below: 

* [Session Based Authentication Implementation]()
* [JWT Based Authentication Implementation]()

## Authentication Choices

{% asset_img auth-types.PNG %}

Above is a high level overview of the main authentication choices available to developers today.  Here is a quick overview of each: 

* Session Based Authentication - Utilizes browser Cookies along with backend "Sessions" to manage logged-in and logged-out users.
* JWT Authentication - A stateless authentication method where a JSON Web token (JWT) is stored in the browser (usually `localStorage`).  This JWT has assertions about a user and can only be decoded using a secret that is stored on the server.
* OAuth and OpenID Connect Authentication - A modern authentication method where an application uses "claims" generated from other applications to authenticate its own users.  In other words, this is federated authentication where an existing service (like Google) handles the authentication and storage of users while your application leverages this flow to authenticate users.

One note I'll make--Oauth can become confusing really quickly, and therefore is not fully explored in this post.  Not only is it unecessary for a small team/startup getting an application off the ground, but it is also highly variable depending on which service you are using (i.e. Google, Facebook, Github, etc.).

Finally, you might notice that OAuth is listed as "As a service" and "In house".  This is a specific note made to highlight the fact that there is actually a company called "OAuth" that implements the OAuth protocol... As a service.  You can implement the OAuth protocol without using OAuth the company's service!

## What is Session Based Authentication?

If we were to create a lineage for these authentication methods, session based authentication would be the oldest of them all, but certainly not obsolete.  This method of authentication is "server-side", which means our Express application and database work together to keep the current authentication status of each user that visits our application.

To understand the basic tenets of session-based-authentication, you need to understand a few concepts: 

* Basic HTTP Header Protocol
* What a cookie is
* What a session is
* How the session (server) and cookie (browser) interact to authenticate a user

### HTTP Headers

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

### How Cookies Work

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

### The Reality of this Situation

Obviously, what I have just described would be a highly insecure way to authenticate a user.  In reality, the server would create some sort of hash from the password the user provided, and validate that hash with some crypto library on the server.

That said, the high-level concept is valid, and it allows us to understand the value of cookies when talking about authentication.

Keep this example in mind as we move through the remainder of this post.

### Sessions

Sessions and cookies are actually quite similar, and can get confused because they can actually be used _together_ quite seamlessly.  The _main difference_ between the two is the **location** of their storage.  

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
npm install --save express mongoose dotenv connect-mongo express-session passport passport-local
```

Here is `app.js`.  Read through the comments to learn more about what is going on before continuing.

```javascript
const express = require('express');
const mongoose = require('mongoose');
const session = require('express-session');

// Package documentation - https://www.npmjs.com/package/connect-mongo
const MongoStore = require('connect-mongo')(session);





/**
 * -------------- GENERAL SETUP ----------------
 */

// Gives us access to variables set in the .env file via `process.env.VARIABLE_NAME` syntax
require('dotenv').config();

// Create the Express application
var app = express();


// Middleware that allows Express to parse through both JSON and x-www-form-urlencoded request bodies
// These are the same as `bodyParser` - you probably would see bodyParser put here in most apps
app.use(express.json());
app.use(express.urlencoded({extended: true}));



/**
 * -------------- DATABASE ----------------
 */

/**
 * Connect to MongoDB Server using the connection string in the `.env` file.  To implement this, place the following
 * string into the `.env` file
 * 
 * DB_STRING=mongodb://<user>:<password>@localhost:27017/database_name
 */ 
const connection = mongoose.createConnection(process.env.DB_STRING);

// Creates simple schema for a User.  The hash and salt are derived from the user's given password when they register
const UserSchema = new mongoose.Schema({
    username: String,
    hash: String,
    salt: String
});

// Defines the model that we will use in the app
mongoose.model('User', UserSchema);





/**
 * -------------- SESSION SETUP ----------------
 */

/**
 * The MongoStore is used to store session data.  We will learn more about this in the post.
 * 
 * Note that the `connection` used for the MongoStore is the same connection that we are using above
 */
const sessionStore = new MongoStore({ mongooseConnection: connection, collection: 'sessions' })

/**
 * See the documentation for all possible options - https://www.npmjs.com/package/express-session
 * 
 * As a brief overview (we will add more later): 
 * 
 * secret: This is a random string that will be used to "authenticate" the session.  In a production environment,
 * you would want to set this to a long, randomly generated string
 * 
 * resave: when set to true, this will force the session to save even if nothing changed.  If you don't set this, 
 * the app will still run but you will get a warning in the terminal
 * 
 * saveUninitialized: Similar to resave, when set true, this forces the session to be saved even if it is unitialized
 */
app.use(session({
    secret: process.env.SECRET,
    resave: false,
    saveUninitialized: true,
    store: sessionStore 
}));





/**
 * -------------- ROUTES ----------------
 */

// When you visit http://localhost:3000/login, you will see "Login Page"
app.get('/login', (req, res, next) => {

    res.send('<h1>Login Page</h1>');

});

app.post('/login', (req, res, next) => {



});

// When you visit http://localhost:3000/register, you will see "Register Page"
app.get('/register', (req, res, next) => {

    res.send('<h1>Register Page</h1>');
    
});

app.post('/register', (req, res, next) => {



});







/**
 * -------------- SERVER ----------------
 */

// Server listens on http://localhost:3000
app.listen(3000);
```

The first thing we need to do is understand how the `express-session` module is working within this application.  This is a "middleware", which is a fancy way of saying that it is a function that modifies something in our application.  

### Quick Refresher on Express Middleware

Let's say we had the following code: 

```javascript
const express = require('express');

var app = express();

// Custom middleware
function myMiddleware1(req, res, next) {
    req.newProperty = 'my custom property';
    next();
}

// Another custom middleware
function myMiddleware2(req, res, next) {
    req.newProperty = 'updated value';
    next();
}

app.get('/', (req, res, next) => {
    res.send(`<h1>Custom Property Value: ${req.newProperty}`);
});

// Server listens on http://localhost:3000
app.listen(3000);
```

As you can see, this is an extremely simple Express application that defines two middlewares and has a single route that you can visit in your browser at `http://localhost:3000`.  If you started this application and visited that route, it would say "Custom Property Value: undefined" because defining middleware functions alone is not enough.

We need to tell the Express application to actually use these middlewares.  We can do this in a few ways.  First, we can do it within a route.

```javascript
app.get('/', myMiddleware1, (req, res, next) => {
    res.send(`<h1>Custom Property Value: ${req.newProperty}`);
});
```

If you add the first middleware function as an argument to the route, you will now see "Custom Property Value: my custom property" show up in the browser.  What really happened here: 

1. The application was initialized
2. A user visited `http://localhost:3000/` in the browser, which triggered the `app.get()` function.
3. The Express application first checked to see if there was any "global" middleware installed on the router, but it didn't find any.
4. The Express application looked at the `app.get()` function and noticed that there was a middleware function installed before the callback.  The application ran the middleware and passed the middleware the `req` object, `res` object, and the `next()` callback.
5. The `myMiddleware1` middleware first set `req.newProperty`, and then called `next()`, which tells the Express application "Go to the next middleware".  If the middleware did not call `next()`, the browser would get "stuck" and not return anything.
6. The Express app did not see any more middleware, so it continued with the request and sent the result.

This is just one way to use middleware, and it is exactly how the `passport.authenticate()` function (more on this later, so keep in mind) works.

Another way we can use middleware is by setting it "globally".  Take a look at our app after this change:

```javascript
const express = require('express');

var app = express();

// Custom middleware
function myMiddleware1(req, res, next) {
    req.newProperty = 'my custom property';
    next();
}

// Another custom middleware
function myMiddleware2(req, res, next) {
    req.newProperty = 'updated value';
    next();
}

app.use(myMiddleware2);

app.get('/', myMiddleware1, (req, res, next) => {
    // Sends "Custom Property Value: my custom property
    res.send(`<h1>Custom Property Value: ${req.newProperty}`);
});

// Server listens on http://localhost:3000
app.listen(3000);
```

With this app structure, you will notice that visiting `http://localhost:3000/` in the browser _still_ returns the same value as before.  This is because the `app.use(myMiddleware2)` middleware is happening _before_ the `app.get('/', myMiddleware1)`.  If we removed the middleware from the route, you will see the updated value in the browser. 

```javascript
app.use(myMiddleware2);

app.get('/', (req, res, next) => {
    // Sends "Custom Property Value: updated value
    res.send(`<h1>Custom Property Value: ${req.newProperty}`);
});
```

We could also get this result by placing the second middleware after the first within the route.

```javascript
app.get('/', myMiddleware1, myMiddleware2, (req, res, next) => {
    // Sends "Custom Property Value: updated value
    res.send(`<h1>Custom Property Value: ${req.newProperty}`);
});
```

Although this is a quick and high-level overview of middleware in Express, it will help us understand what is going on with the `express-session` middleware.

### How Express Session Middleware works

As I mentioned before, the `express-session` module gives us middleware that we can use in our application.  The middleware is defined in this line: 

```javascript
// Again, here is the documentation for this - https://www.npmjs.com/package/express-session
app.use(session({
    secret: process.env.SECRET,
    resave: false,
    saveUninitialized: true,
    store: sessionStore 
}));
```

Here is a brief overview of what the Express Session Middleware is doing:

1. When a route is loaded, the middleware checks to see if there is a session established in the Session Store (MongoDB database in our case since we are using the `connect-mongo` custom Session Store).  
2. If there is a session, the middleware validates it cryptographically and then tells the Browser whether the session is valid or not.  If it is valid, the Browser automatically attaches the `connect.sid` Cookie to the HTTP request.
3. If there is no session, the middleware creates a new session, takes a cryptographic hash of the session, and stores that value in a Cookie called `connect.sid`.  It then attaches the `Set-Cookie` HTTP header to the `res` object with the hashed value (`Set-Cookie: connect.sid=hashed value`).

You might be wondering why this is useful at all, and how all this actually works.

If you remember from the quick refresher on Express Middlewares, I said that a middleware has the ability to alter the `req` and `res` objects that are passed from one middleware to the next until it reaches the end of the HTTP request.  Just like we set a custom property on the `req` object, we could also set something much more complex like a `session` object that has properties, methods, etc.

That is exactly what the `express-session` middleware does.  When a new session is created, the following properties are added to the `req` object: 

* `req.sessionID` - A randomly generated UUID.  You can define a custom function to generate this ID by setting the `genid` option.  If you do not set this option, the default is to use the `uid-safe` [module](https://www.npmjs.com/package/uid-safe).

```javascript
app.use(session({
  genid: function (req) {
    // Put your UUID implementation here
  }
}));
```

* `req.session` - The Session object.  This contains information about the session and is available for setting custom properties to use.  For example, maybe you want to track how many times a particular page is loaded in a single session: 

```javascript 
app.get('/tracking-route', (req, res, next) => {
  
  if (req.session.viewCount) {
    req.session.viewCount = req.session.viewCount + 1;
  } else {
    req.session.viewCount = 1;
  }

  res.send("<p>View count is: " + req.session.viewCount + "</p>");
  
});
```

* `req.session.cookie` - The Cookie object.  This defines the behaviour of the cookie that stores the hashed session ID in the browser.  Remember, once the cookie has been set, the browser will attach it to every HTTP request automatically until it expires.

### How Passport JS Local Strategy works

There is one last thing that we need to learn in order to fully understand Session Based Authentication--Passport JS.

Passport JS has over 500 authentication "Strategies" that can be used within a Node/Express app.  Many of these strategies are highly specific (i.e. `passport-amazon` allows you to authenticate into your app via Amazon credentials), but they all work similar within your Express app.

In my opinion, the Passport module could use some work in the department of documentation.  Not only does Passport consist of two modules (Passport base + Specific Strategy), but it is also a middleware, which as we saw is a bit confusing in its own right.  To add to the confusion, the strategy that we are going to walk through (`passport-local`) is a middleware that modifies an object created by another middleware (`express-session`).  Since the Passport documentation has little to say around how this all works, I will attempt to explain it to the best of my ability in this post.

Let's first walk through the setup of the module.

If you have been following along with this tutorial, you already have the modules needed.  If not, you will need to install Passport and a Strategy to your project.

```
npm install --save passport passport-local
```

Once you have done that, add Passport to your existing application.

```javascript 
const express = require('express');
const mongoose = require('mongoose');
const session = require('express-session');
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;

const MongoStore = require('connect-mongo')(session);

require('dotenv').config();

var app = express();

const connection = mongoose.createConnection(process.env.DB_STRING);

const UserSchema = new mongoose.Schema({
    username: String,
    hash: String,
    salt: String
});

// Defines the model that we will use in the app
mongoose.model('User', UserSchema);

const sessionStore = new MongoStore({ mongooseConnection: connection, collection: 'sessions' })

app.use(session({
    secret: process.env.SECRET,
    resave: false,
    saveUninitialized: true,
    store: sessionStore 
}));




/**
 * -------------- PASSPORT ----------------
 */
app.use(passport.initialize());
app.use(passport.session());




/**
 * -------------- ROUTES ----------------
 */

// When you visit http://localhost:3000/login, you will see "Login Page"
app.get('/login', (req, res, next) => {

    res.send('<h1>Login Page</h1>');

});

app.post('/login', (req, res, next) => {



});

// When you visit http://localhost:3000/register, you will see "Register Page"
app.get('/register', (req, res, next) => {

    res.send('<h1>Register Page</h1>');
    
});

app.post('/register', (req, res, next) => {



});







/**
 * -------------- SERVER ----------------
 */

// Server listens on http://localhost:3000
app.listen(3000);
```

### Conceptual Overview of Session Based Authentication

Now that we understand HTTP Headers, Cookies, Middleware, Express Session middleware, and Passport JS middleware, it is finally time to learn how to use these to authenticate users into our application.  I want to first use this section to review and explain the conceptual flow, and then dive into the implementation in the next section.



### Session Based Authentication Implementation 

This section will walk through a basic implementation of the Passport Local Strategy, and should be usable in production (although I would recommend refactoring, adding additional error handling, and other features like ensuring that there are no duplicate users).


























https://developer.mozilla.org/en-US/docs/Web/HTTP/Session
https://stackoverflow.com/questions/11142882/what-are-cookies-and-sessions-and-how-do-they-relate-to-each-other
https://hueniverse.com/introducing-oauth-2-0-b5681da60ce2
http://www.passportjs.org/packages/passport-jwt/
http://www.passportjs.org/packages/passport-auth0/

## Implementation


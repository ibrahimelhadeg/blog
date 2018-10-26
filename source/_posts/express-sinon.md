---
title: Writing SinonJS Unit Tests for an Express Application
date: 2018-10-20 16:57:37
tags: sinon
---

Please [refer to this Github repo](https://github.com/zachgoll/sinon-unit-testing-example) for all code contained in this post.

One of the hardest topics to grasp in coding is that of testing.  Despite what many seasoned developers might say, testing does not come naturally to anyone--especially when your code has nested callbacks, promises, and external dependencies that behave unexpectedly.  As a self-taught developer, it took me a while to understand the benefits of testing code, and even longer to finally dedicate myself to learning the art of testing.  Although I am no seasoned testing engineer, I wanted to share a quick tutorial on how to test one of the most common types of applications for any web developer--an Express app.

In this tutorial, I will go through the following topics.

1. A Brief Introduction to SinonJS
2. Getting your Express App ready for testing (requires small restructuring)
3. Writing Unit Tests for basic functions 
4. Writing Unit Tests for Express routes
5. Debugging Unit Tests (often overlooked, but extremely useful!)

# Brief Introduction to SinonJS

Recreating the wheel is not my intention for this post, so I will not cover the basics of using SinonJS but rather refer readers to [Jani Hartikainen's guide on SinonJS](Jani Hartikainen) (I have no relationship with Jani nor receive affiliate revenue for promoting this guide--it's just really good and worth recommending.).  It is ever so slightly outtdated, but it covers 99% of the things that you might need to know about using SinonJS for javascript unit testing.

In a nutshell, SinonJS allows you to test the code written below easily.

```javascript
const MyExternalLibrary = require('./external-library');
const AnotherLibrary = require('./another-library');

class MyCustomClass {
    constructor(numbers) {
        this.numbers = numbers;
    }

    calculateAverage() {
        const nums = this.numbers;
        if (AnotherLibrary.validateNums(nums)) {
            return MyExternalLibrary.calcAvg(this.numbers);
        } else {
            throw new Error('Numbers not valid');
        }
    }
}

module.exports = MyCustomClass;
```

There is no significance to the example above; it is entirely made up and random.  If we wanted to test the `calculateAverage()` method of `MyCustomClass`, we need to ensure that the external libraries used by this method do not interfere with what we are actually testing.  The goal of unit testing the `calculateAverage()` method is to make sure that given some inputs, we receive desired outputs.  We do not care how `AnotherLibrary.validateNums()` or `MyExternalLibrary.calcAvg()` works.  We need to make sure that these two _external_ methods behave how we want them to.  The only thing we care about is the following:

1. If `this.numbers` is a _valid_ array of numbers (i.e. does not have any text characters or special characters), we expect `calculateAverage()` to return the average of this array of numbers.
2. If `this.numbers` is _invalid_, we expect that `calculateAverage()` will throw an error.

We don't care to know if the average that is returned by `MyExternalLibrary.calcAvg()` is actually correct.  We would check that fact in a separate unit test.

This is where SinonJS comes in.  Sinon allows us to "stub" both `AnotherLibrary.validateNums()` and `MyExternalLibrary.calcAvg()` and force them to return the values that we desire.  In this specific example, we would probably want to write two separate unit tests.  First, we want to test the case where `AnotherLibrary.validateNums()` returns false, and second, where it returns true.  We can do this easily with Sinon.

```javascript
    describe('My Unit Tests', () => {
        it('When AnotherLibrary.validateNums() returns true, we should receive the average of the numbers array', () => {
            sinon.stub(AnotherLibrary, 'validateNums').returns(true);
            sinon.stub(MyExternalLibrary, 'calcAvg').returns(5);
            
            const myClass = new MyCustomClass([2, 4]);

            const results = myClass.calculateAverage();
            
            // The average of 2 and 4 is not 5, but the reason we make this assertion is because above, we declared that the 
            // MyExternalLibrary.calcAvg() method returns 5, and remember, we don't care if that method actually works correctly here
            expect(results).to.be.equal(5);

            // We altered the behavior of a method for this specific test but need to restore it in case other tests 
            // alter its behavior in a different way
            AnotherLibrary.validateNums.restore();
            MyExternalLibrary.calcAvg.restore();
        });

        it('When AnotherLibrary.validateNums() returns false, we should expect an Error thrown.', () => {
            sinon.stub(AnotherLibrary, 'validateNums').returns(false);

            const myClass = new MyCustomClass([2, 4]);
            const errorMsg = new Error('Numbers not valid');

            const results = myClass.calculateAverage();
            
            // The average of 2 and 4 is 3
            expect(results).to.be.equal(errorMsg);

            // We altered the behavior of a method for this specific test but need to restore it in case other tests 
            // alter its behavior in a different way
            AnotherLibrary.validateNums.restore();
        });
    });
```

Please note that the above code has not been tested and may contain a few syntax errors, but that is not the point.  The point is to convey the usefulness of SinonJS for our testing needs.  We manually controlled the behavior of external libraries so that we can disregard how they work under the hood, and focus on the specific piece of code that we are trying to test.

Again, if you want a full tutorial on how to use SinonJS, I suggest looking at the [documentation](https://sinonjs.org/releases/v7.0.0/) and downloading [Jani's guide](https://codeutopia.net/).

# Preparing your Express Application

Unit testing Express routes can be challenging; especially if your application is not setup in a way that allows for modular testing.  We are going to refactor our Express app so that testing it is much easier (idea credit comes from [Evan Shortiss's Blog](http://evanshortiss.com/development/javascript/2016/04/15/express-testing-using-ioc.html)).

Most Express applications are built in the following way.

```javascript
/**
 * File: app.js
 */
const express = require('express');
const config = require('./config');

// Set up the Express app object
const app = express();

// Grab the port number from some config file
const port = config.port || 3000;

// Tell the app to use the user-routes.js file for routing
app.use('/', require('./user-routes'));

// Serve the app 
app.listen(port, () => {
    console.log(`App listening on ${port}`);
});

// Export the app object for other files to use
module.exports = app;
```

```javascript
/**
 * File: user-routes.js
 */

// Require the Express Router object
const router = require('express').Router();

// At http://HOST/api, use the routes found in the api/ directory
router.use('/api', require('./api'));

// At http://HOST/, you will see 'This is the home page' displayed 
router.get('/', (req, res, next) => {
    res.send('This is the home page');
});

module.exports = router;
```

Most Express applications use this simple structure, and honestly, it is the simplest way to go.  But if you are looking to test your Express application, this poses a problem, because as you may notice, we are splitting our routes into many different files that all depend on each other to function.  Therefore, if I wanted to test one of my api routes, I would have to require in the `routes/index.js` file as well.  The goal of the refactor that we are going to do is to eliminate this chain of route dependencies, and create the ability to test one route file at a time.  How do we go about this?  We need to turn all of our route files into exported functions that take the Express app object as arguments.  Below is a simple refactor of the above app.

```javascript
/**
 * app.js 
 */
const express = require('express');
const config = require('./config');
const routes = require('./user-routes');

// Set up the Express app object
const app = express();

// Grab the port number from some config file
const port = config.port || 3000;

// THIS LINE IS THE ONLY DIFFERENCE!
routes(app);

// Serve the app 
app.listen(port, () => {
    console.log(`App listening on ${port}`);
});

// Export the app object for other files to use
module.exports = app;
```

```javascript
/**
 * File: user-routes.js
 */

// Require the Express Router object
const router = require('express').Router();

// Require the api routes file
const apiRoutes = require('./api');

module.exports = (app) => {
    app.use('/', router);

    apiRoutes(app);

    router.get('/', (req, res, next) => {
        res.send('This is the home page');
    });
}
```

I know the above code may appear confusing at first, but please take your time reading into it and understanding exactly what is happening.  After adjusting your way of thinking, this method will seem a lot less complicated than it initially appears.

Once you have refactored all of your routes to look like this, you are ready to start testing!

# Writing Unit Tests for Basic functions

In order to demonstrate the full capability of SinonJS in testing Express applications, we are going to start with some basic functions to test, which will eventually play a role in our route tests.  Below are the files of a simple Express application that we will test.  I suggest reading through and making yourself familiar with the structure before proceeding.  Please note that all files are located in the same directory for ease of example (Most Express apps would be split into folders).

```javascript
/**
 * app.js
 */
const express = require('express');
const config = require('./config');
const routes = require('./user-routes');
const databaseConfig = require('./config/database');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');

// Set up the Express app object
const app = express();

// Using Mongoose as an interface to our MongoDB database
mongoose.connect(databaseConfig.database, { useNewUrlParser: true });

let connection = mongoose.connection;

connection.on('connected', () => { logger.info(`App connected to database ${databaseConfig.dbName}`) });
connection.on('error', (err) => { logger.error(`Database error: ${err}`) });

// Grab the port number from some config file
const port = config.port || 3000;

// Use the body parser for easily interpreting requests with json
app.use(bodyParser.json());

// THIS LINE IS THE ONLY DIFFERENCE!
routes(app);

// Serve the app 
app.listen(port, () => {
    console.log(`App listening on ${port}`);
});

// Export the app object for other files to use
module.exports = app;
```

```javascript
/**
 * user-routes.js
 */
// Require the Express Router object
const router = require('express').Router();
const User = require('./user-model');

module.exports = (app) => {
    app.use('/', router);

    router.get('/', (req, res, next) => {
        res.send('This is the home page');
    });
}
```

```javascript
/**
 * user-model.js
 */
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

// User schema for mongodb
const UserSchema = mongoose.Schema({
	name: {
		type: String
	},
	email: {
		type: String,
		required: true
    },
    password: {
        type: String,
        required: true
    }
});

const User = module.exports = mongoose.model('User', UserSchema);

module.exports.getUserById = (id, callback) => {
	try {
		User.findById(id, callback);
	} catch (err) {
		callback(err);
	}
}

module.exports.addUser = (newUser, callback) => {
	bcrypt.genSalt(10, (err, salt) => {
		bcrypt.hash(newUser.password, salt, (err, hash) => {
			if (err) throw err;
			newUser.password = hash;
			newUser.save(callback);
		});
	});
}

module.exports.comparePassword = (password, hash, callback) => {
	bcrypt.compare(password, hash, (err, isMatch) => {
		callback(err, isMatch);
	});
}
```

_Please note that the routes file is incomplete and I will be adding more routes to it as we move forward._

This application is as simple as it gets.  I am using MongoDB as my database, and my only collection is the users collection.  To clarify things further, we can see that the `user-model.js` file exports a Mongoose model and a few helper functions for authentication.  When we import this file into a route file, we will get an object that functions like a class object with static methods.

```javascript
class User {
    static comparePassword () {
    }
     
    static addUser() {
    }
    
    static getUserById() {
    }

    // Constructor to the Mongoose.Model object takes the doc name and fields
    constructor(doc, fields) {
    }

    // These methods are part of the Mongoose.Model object and we did not create them
    save() {
    }

    // These methods are part of the Mongoose.Model object and we did not create them
    findById(){
    }

    // Other methods found at https://mongoosejs.com/docs/api.html#Model
}
```

The above exercise is not necessary, but I think it helps one further understand how our `user-model.js` file is functioning.

The first step in testing this application is to write unit tests for the `user-model.js` file.  Looking at the file, we want to ensure that: 

1. User model exists
2. getUserById() works correctly 
3. addUser() works correctly
4. comparePassword() works correctly

I know all of these functions are rather simple, but it is still important to test them, and they will be great starter examples.

## Setting up the test file

Before testing, we need to setup our testing environment.  We will add a file called `all-tests.js`.  Again, this would usually be split into multiple files in the `test/` folder, but we are keeping everything in one file to avoid too much complexity.  For our testing purposes, we will be using the Chai Expect assertion library for our tests.  Here is the basic setup for our testing.

```javascript 
/**
 * File: all-tests.js 
 */

const bcrypt = require('bcryptjs');
const sinon = require('sinon');
const User = require('./user-model');

// More verbose assertion library
const { expect } = require('chai');

describe('User Model Tests', () => {

    beforeEach(() => {
        
    });

    afterEach(() => {
    });

    it ('User model exists', () => {
        
    });

    it('When getUserById() is a success, we should receive the user from the db', () => {

    });

    it('When getUserById() fails, we should receive an error'), () => {

    };

    it('When addUser() is a success, the user should be added to the database with hash as password', () => {

    });
 
    it('When addUser() is a failure, the user will not be added to the database and we will get error', () => {

    });

    it('When password matches hash, comparePassword() will return true', () => {

    });

    it('When password does not match hash, comparePassword() will return false', () => {

    });
});
```

The `all-tests.js` file will eventually contain both our User model tests and our User routes tests, but for now, we just have the User model tests.  As you can see, we have several tests for just a few methods.  This is not always necessary, but is appropriate for this tutorial.  A few things to point out in the code above:

* You can see that many of the unit tests have a keyword called `done`.  This is part of the Mocha testing library, and allows you to tell Mocha when a specific asynchronous function has completed.  This will come in handy since we are dealing with callbacks and http requests.
* Notice there is a `beforeEach()` and `afterEach()` function at the top of the describe block.  With Mocha, these functions will be called before each of the unit tests and after each of the unit tests.  They are useful for setting up things that are needed for each and every test.
* We have required in a few external libraries like bcrypt.js.  This is because our `user-model.js` file uses this library and we will need to "stub" it out for our tests.
* Quick tip for actually running the tests:  Go to your `package.json` file and add the mocha command to your test scripts using nodemon.  This allows the tests to reload automatically after you update the test files.

```
"scripts": {
"test": "nodemon --exec 'mocha test'"
},
```

## Testing user-model.js 

We are ready to test our functions, but one of the biggest challenges in unit testing is understanding __what__ you are trying to test.  We will start with the simplest one.  We want to make sure that the User model exists when we require it into a file.  To test this, we will simply write the following (please note from here on out, I will only be including the relevant snippet of code rather than the entire test file).

```javascript
const User = require('./user-model');

it ('User model exists', () => {
    expect(User.model).to.exist;
    expect(User.collection.name).to.equal('users');
});
```

The reason we test this is primarily to make sure that we are requiring in the correct Model.  We make sure that the model object exists and make sure that one of its properties equals what we would expect (I chose the collection name because this should not change).  This initial test will also get us on the right foot and confirm that our test suite is working correctly in general.

Next, we want to test the `getUserById()` function.  

```javascript
const User = require('./user-model');

it('When getUserById() is a success, we should receive the user from the db', () => {
    
    // We first set up a dummy user that we will force as the return value of User.findById
    const expectedUser = new User({
        name: 'Firstname',
        email: 'firstname@email.com',
        password: 'somepassword'
    });

    // Alter the behavior of User.findById() to return our expectedUser
    sinon.stub(User, 'findById').yieldsAsync(expectedUser);

    // Call the actual function we are unit testing
    User.getUserById('12345', function(user) {

        // We expect that the user returned from our function will equal 
        // the user we made it return
        expect(user).to.equal(expectedUser);
    });

    // Asserting that the method called as User.findById('12345');
    sinon.assert.calledWith(User.findById, '12345');

    // Always remember to restore the functionality of your functions or other unit tests will break!
    User.findById.restore();
});

it('When getUserById() fails, we should receive an error', () => {
    // Create stub on method and force to throw an error
    sinon.stub(User, 'findById');
    const expectedError = new Error('could not find user');
    User.findById.throws(expectedError);

    // We use a Sinon spy because we want to know the parameter that the callback used
    const callback = sinon.spy();

    // Call the function we are testing
    User.getUserById('12345', callback);

    // Asserting that the callback is called with the expectedError as a parameter
    sinon.assert.calledWith(callback, expectedError);

    // Always remember to restore the functionality of your functions or other unit tests will break!
    User.findById.restore();
});
```

I know some of the above code looks a bit intimidating with "stubs" and "spies", but if you brush up on your SinonJS, you will quickly learn what exactly is going on.  The real topic I want to cover here is the _why_ behind things, and the methodology.  In the first unit test, we assumed that our function was called with some parameters and did not throw any errors.  We made sure that given some inputs, we receive our expected outputs.  It may seem redundant to force some method to return a specific value, but it is a great way to test the core functionality of a method.  In the second test, we considered the only other scenario that might happen in our code.  What if the method throws an error?  Have we handled it?  In this case, yes, we have handled the error with a `try {} catch {}` block.  In many cases, writing unit tests forces us to write clean and resilient code.  Before I wrote the unit test for this particular method, I did not have a try/catch block, which means that if my app has trouble connecting to the database and throws an errror, it will crash.  By testing for this error scenario, I realized that I needed to handle this error condition.

I will spend less time on the remaining two methods, but here they are.

```javascript
const User = require('./user-model');
const bcrypt = require('bcryptjs');

it('When addUser() is a success, the user should be added to the database with hash as password', (done) => {

    // Create a dummy user
    const user = new User({
        name: 'some name', 
        password: 'some password'
    });

    // Call the function we are testing with the dummy user as an argument
    User.addUser(user, function() {

        // We expect the original password to not match the new one because this method 
        // should transform the password into a secure hash for storage in the database
        expect(user.password).to.not.equal('some password');

        // Be sure to call done to tell Mocha that the asynchronous execution has finished
        done();
    });
});

it('When addUser() is a failure, the user will not be added to the database and we will get error', () => {
    
    // Create dummy user
    const user = new User({
        name: 'some name', 
        password: 'some password'
    });

    // Create an error to throw
    const expectedError = new Error('some error');
    
    // Force the bcrypt.genSalt() method to throw our expected error
    sinon.stub(bcrypt, 'genSalt').throws(expectedError);
    
    // Call the function we are testing in a try/catch block, and assert that we arrived at the catch block
    // with the expected error
    try {
        User.addUser(user, function() {});
    } catch (err) {
        sinon.assert.threw(bcrypt.genSalt, expectedError);
    }
    
    // Remember to restore!
    bcrypt.genSalt.restore();
});

it('When password matches hash, comparePassword() will return true', (done) => {
    const password = 'some password';
    const salt = bcrypt.genSaltSync(10);
    const hash = bcrypt.hashSync(password, salt);
    
    // No need to create any stubs.  Just call the method and check that the results are 
    // as expected.  We expect that given the above inputs, our password will be a match
    User.comparePassword(password, hash, (err, isMatch) => {
        expect(isMatch).to.be.true;
        expect(err).to.be.null;
        done();
    });
});

it('When password does not match hash, comparePassword() will return false', (done) => {
    
    // Create our expected error
    const expectedError = new Error('comparison did not work');

    // Create a stub on the bcrypt.compare() method and force it to throw our expectedError
    sinon.stub(bcrypt, 'compare').yieldsAsync(expectedError);
    
    // Create a spy on the callback so we can determine what arguments it was called with
    const callback = sinon.spy();

    // Call the function we are testing with the spy callback, and make sure to call done() to 
    // let Mocha know that the async function has completed
    User.comparePassword('some password', 'some salt', callback);
    done();

    // We expect that the callback will be called once with the expectedError as an argument
    sinon.assert.calledOnce(callback);
    sinon.assert.calledWith(callback, expectedError);

    // Remember to restore!
    bcrypt.compare.restore();
});
```

As you can see, there is a common pattern with our tests.  We setup the conditions we want to use as _inputs_ for our test, we call the function we are testing, and we assert that the _outputs_ are what we should expect based on the inputs.  This ensures that all of our functions perform predictably, and if we enhance the code, add a feature, etc., we know that this new feature has not broken anything in our functions.

# Writing Unit Tests for Express routes

Writing unit tests for Express routes is a bit trickier than simple utility functions like the ones above.  The reason is due to nested, asynchronous calls in the form of callbacks and promises.  Testing Express routes takes some patience and requires the testing engineer to break each route into many steps; stubbing functions along the way.  Before we get started, let's look at the routes we will be testing and get a preliminary idea of how we might test them.

```javascript
/**
 * user-routes.js
 */
// Require the Express Router object
const router = require('express').Router();
const User = require('./user-model');

module.exports = (app) => {
    app.use('/', router);

    router.get('/', (req, res, next) => {
        res.send('This is the home page');
    });

    // Register a user
    router.post('/register', (req, res, next) => {

        let newUser = new User({
            name: req.body.name,
            email: req.body.email,
            password: req.body.password
        });

        User.addUser(newUser, (err, user) => {
            if (err){
                res.json({success: false, msg: 'Failed to register'});
            } else {
                res.json({success: true, msg: 'User registered!'});
            }
        });
    });

    // Get a user
    router.get('/:id', (req, res, next) => {

        User.getUserById(req.params.id, (err, user) => {
            if (err) next(err);
            if (!user){
                return res.json({success: false, msg: "Failed to authenticate user"});
            } else {
                return res.json({success: true, msg: user });
            }
        });
    });

    // Authenticate and login a user based on id and password
    router.post('/authenticate/:id', (req, res, next) => {

        const password = req.body.password;
        const id = req.params.id;

        User.getUserById(id, (err, user) => {
            if (err) next(err);

            if (!user){
                return res.json({success: false, msg: "Failed to authenticate user"});
            }

            User.comparePassword(password, user.password, (err, isMatch) => {
                if(err) next(err);
                if(isMatch){

                    // Token expires in 1 month = 2,629,746 seconds
                    const token = jwt.sign({data: user}, databaseConfig.secret, {
                        expiresIn: 2629746
                    });

                    res.json({
                        success: true,
                        token: 'Bearer ' + token,
                        user: {
                            id: user._id,
                            name: user.name,
                            email: user.email
                        }
                    });
                } else{
                    return res.json({success: false, msg: "Wrong password"});
                }
            });
        });
    });
}
```

We have three main routes to our applicaton.

1. `/register`
2. `/:id`
3. `/authenticate/:id`

The register route will use our `User.addUser()` method to add a user to the database.  The id route will perform a get request and use the `User.getUserById()` method to retrieve an existing user from the database.  Finally, the authenticate route will use the `User.compare()` method to check the login credentials of a user that is trying to login.

Let's walk through each of these routes and discuss a strategy as to how we might test them.

## Register route 

```javascript
// Register a user
router.post('/register', (req, res, next) => {

    let newUser = new User({
        name: req.body.name,
        email: req.body.email,
        password: req.body.password
    });

    User.addUser(newUser, (err, user) => {
        if (err){
            res.json({success: false, msg: 'Failed to register'});
        } else {
            res.json({success: true, msg: 'User registered!'});
        }
    });
});
```

The register route can be broken into two distinct parts.  First, we _instantiate_ a new _instance_ of the User class at the beginning of the route.  We then take this class instance and pass it to our `User.addUser()` method, which will return with one of two json responses.  At first glance, we know that we are going to need a unit test for the successful condition and an error condition.  We also see that since the route is using `User.addUser()` (which we already tested and don't care about here), we will need to "stub" this method and force it to behave in a certain.

## Get by ID route 

```javascript
// Get a user
router.get('/:id', (req, res, next) => {

    User.getUserById(req.params.id, (err, user) => {
        if (err) next(err);
        if (!user){
            return res.json({success: false, msg: "Failed to authenticate user"});
        } else {
            return res.json({success: true, msg: user });
        }
    });
});
```

Arguably the simplest of the three routes, this route uses the `User.getUserById()` method to retrieve a user based on his/her ID from the database.  As with the previous route, we have the `getUserById()` method which we have already tested.  Since we already tested it, we don't care to know if it works in this test, so we need to "stub" it out and force it to behave a certain way (success, error).

## Authenticate by ID and password route 

```javascript
// Authenticate and login a user based on id and password
router.post('/authenticate/:id', (req, res, next) => {

    const password = req.body.password;
    const id = req.params.id;

    User.getUserById(id, (err, user) => {
        if (err) next(err);

        if (!user){
            return res.json({success: false, msg: "Failed to authenticate user"});
        }

        User.comparePassword(password, user.password, (err, isMatch) => {
            if(err) next(err);
            if(isMatch){

                // Token expires in 1 month = 2,629,746 seconds
                const token = jwt.sign({data: user}, databaseConfig.secret, {
                    expiresIn: 2629746
                });

                res.json({
                    success: true,
                    token: 'Bearer ' + token,
                    user: {
                        id: user._id,
                        name: user.name,
                        email: user.email
                    }
                });
            } else{
                return res.json({success: false, msg: "Wrong password"});
            }
        });
    });
});
```

This is the most difficult route to test because it uses two separate external libraries.  One of the libraries is asynchronous (the `User.compare()` method) while the other is synchronous (the `jwt.sign()` method).  We will need to remember these details when testing.  For this unit test, we will need to create Sinon stubs for both of these methods.

## Writing the tests 

We have gone through each of the routes, but have not yet discussed how we are going to perform the actual unit tests.  How are we going to simulate an HTTP request in a testing environment?   Well, this is where our `supertest` library comes in handy.  For each of our tests, we are going to create a minimal Express application and use the `supertest` library to simulate each route call.  Here is what our test file should look like at this point (Notice that we have placed the route tests in a new Describe block).

```javascript
/**
 * File: all-tests.js 
 */

const bcrypt = require('bcryptjs');
const sinon = require('sinon');
const express = require('express');
const User = require('./user-model');
const routes = require('./user-routes');
const bodyParser = require('body-parser');

const supertest = require('supertest');

// More verbose assertion library
const { expect } = require('chai');

describe('User Route Tests', () => {
    let app, request;
    beforeEach(() => {

        // Create an express application object
        app = express();
        app.use(bodyParser.json());

        // Bind our routes to our application using the method explained earlier
        routes(app);

        // Add newly created app instance to supertest object
        request = supertest(app);
    });

    afterEach(() => {

    });
    
    it('User can register successfully at the /register route', (done) => {

    })

    it('/register route fails', (done) => {

    })

    it('Can get user from /:id route', (done) => {

    })

    it('Cannot get user from /:id route -- User with id does not exist', (done) => {

    })

    it('User is successfully authenticated', (done) => {

    })

    it('User typed in the wrong password', (done) => {

    })
});

describe('User Model Tests', () => {

    beforeEach(() => {
        
    });

    afterEach(() => {
    });

    it('When addUser() is a success, the user should be added to the database with hash as password', (done) => {

        // Create a dummy user
        const user = new User({
            name: 'some name', 
            password: 'some password'
        });

        // Call the function we are testing with the dummy user as an argument
        User.addUser(user, function() {

            // We expect the original password to not match the new one because this method 
            // should transform the password into a secure hash for storage in the database
            expect(user.password).to.not.equal('some password');

            // Be sure to call done to tell Mocha that the asynchronous execution has finished
            done();
        });
    });

    it('When addUser() is a failure, the user will not be added to the database and we will get error', () => {
        
        // Create dummy user
        const user = new User({
            name: 'some name', 
            password: 'some password'
        });

        // Create an error to throw
        const expectedError = new Error('some error');
        
        // Force the bcrypt.genSalt() method to throw our expected error
        sinon.stub(bcrypt, 'genSalt').throws(expectedError);
        
        // Call the function we are testing in a try/catch block, and assert that we arrived at the catch block
        // with the expected error
        try {
            User.addUser(user, function() {});
        } catch (err) {
            sinon.assert.threw(bcrypt.genSalt, expectedError);
        }
        
        // Remember to restore!
        bcrypt.genSalt.restore();
    });

    it('When password matches hash, comparePassword() will return true', (done) => {
        const password = 'some password';
        const salt = bcrypt.genSaltSync(10);
        const hash = bcrypt.hashSync(password, salt);
        
        // No need to create any stubs.  Just call the method and check that the results are 
        // as expected.  We expect that given the above inputs, our password will be a match
        User.comparePassword(password, hash, (err, isMatch) => {
            expect(isMatch).to.be.true;
            expect(err).to.be.null;
            done();
        });
    });

    it('When password does not match hash, comparePassword() will return false', (done) => {
        
        // Create our expected error
        const expectedError = new Error('comparison did not work');

        // Create a stub on the bcrypt.compare() method and force it to throw our expectedError
        sinon.stub(bcrypt, 'compare').yieldsAsync(expectedError);
        
        // Create a spy on the callback so we can determine what arguments it was called with
        const callback = sinon.spy();

        // Call the function we are testing with the spy callback, and make sure to call done() to 
        // let Mocha know that the async function has completed
        User.comparePassword('some password', 'some salt', callback);
        done();

        // We expect that the callback will be called once with the expectedError as an argument
        sinon.assert.calledOnce(callback);
        sinon.assert.calledWith(callback, expectedError);

        // Remember to restore!
        bcrypt.compare.restore();
    });
});
```

Above, I have printed the entire test file all in one.  This includes both route unit tests and User model unit tests.  Let's focus in on the `beforeEach()` block in the route unit test describe block.

```javascript 
beforeEach(() => {

    // Create an express application object
    app = express();
    app.use(bodyParser.json());

    // Bind our routes to our application using the method explained earlier
    routes(app);

    // Add newly created app instance to supertest object
    request = supertest(app);
});
```

As you can see, we are creating minimalistic Express application that has our routes file attached to it, and then we are passing this minimal Express application to the supertest object which will allow us to simulate HTTP requests in each of our unit tests.  With this particular tutorial it is not immediately apparent the usefulness of using an exported function for our routes file, but imagine if we had 10 different route files.  How would we go about testing a single route file when all 10 of them depend on each other?  Let's imagine that we had route files named `routes-1.js`, `routes-2.js`, and `routes-3.js`.  Certainly, we would want to test each of these files one at a time.  With our newly structured Express application, we can.

```javascript 
const routes1 = require('/routes-1');
const routes2 = require('/routes-2');
const routes3 = require('/routes-3');
const express = require('express');
const supertest = require('supertest');
const bodyParser = require('body-parser');

describe('Routes 1 Tests', () => {
    beforeEach(() => {
        app = express();
        app.use(bodyParser.json());
        routes(app);
        request = supertest(app);
    });
});

describe('Routes 2 Tests', () => {
    beforeEach(() => {
        app = express();
        app.use(bodyParser.json());
        routes2(app);
        request = supertest(app);
    });
});

describe('Routes 3 Tests', () => {
    beforeEach(() => {
        app = express();
        app.use(bodyParser.json());
        routes3(app);
        request = supertest(app);
    });
});
```

Having this separation of routes dramatically increases the ease in which we can test each route file.

Finally, it is time to write the tests.  Below are the completed unit tests with comments throughout the code.  I suggest perusing through the comments to understand what is going on better.

```javascript
/**
 * File: all-tests.js 
 */

const bcrypt = require('bcryptjs');
const sinon = require('sinon');
const express = require('express');
const User = require('./user-model');
const routes = require('./user-routes');
const bodyParser = require('body-parser');
const jwt = require('jsonwebtoken');
const config = require('./config');

const supertest = require('supertest');

// More verbose assertion library
const { expect } = require('chai');

describe('User Route Tests', () => {
    let app, request;
    beforeEach(() => {

        // Create an express application object
        app = express();
        app.use(bodyParser.json());

        // Bind our routes to our application using the method explained earlier
        routes(app);

        // Add newly created app instance to supertest object
        request = supertest(app);
    });

    afterEach(() => {

    });

    /**
     * Always a good idea to write a simple test to make sure that you have everything setup 
     * correctly before getting into the more complex tests
     */
    it('Base route works', (done) => {
        request
            .get('/')
            .expect('Content-Type', 'text/html')
            .expect(200, (err, res) => {
                expect(res.text).to.equal('This is the home page');
                done();
            });
    });

    // This makes sure that we are pulling from the correct MongoDB collection
    it ('User exists and belongs to collection users', () => {
        expect(User.model).to.exist;
        expect(User.collection.name).to.equal('users');
    });
    
    
    it('User can register successfully at the /register route', (done) => {
        let newUser = {
            name: 'Steve Jobs',
            email: 'steve@gmail.com',
            password: 'some password'
        };

        const expectedResponse = {
            success: true,
            msg: 'User registered!'
        };

        /**
         * User.addUser() is an async functon, and Sinon does not automatically call the callback
         * We need to call the callback and then let Mocha know that it has completed execution
         */
        sinon.stub(User, 'addUser').callsArg(1);

        request
            .post('/register')
            .send(newUser)
            .set('Accept', 'application/json')
            .expect(200, (err, res) => {

                // Make sure to restore the stub, otherwise other tests will break
                User.addUser.restore();

                // Since we are comparing two objects, we need the Chai Expect library's 
                // deep.equal() method.
                expect(res.body).to.deep.equal(expectedResponse);
                done();
            });
    });

    it('/register route fails', (done) => {
        let newUser = {
            invalidProperty: 'Steve Jobs'
        };

        const expectedResponse = {
            success: false,
            msg: 'Failed to register'
        };

        // We need to call the addUser() method with an error because the user we are
        // trying to add is invalid
        sinon.stub(User, 'addUser').callsArgWith(1, new Error('Route threw error'));

        request
            .post('/register')
            .send(newUser)
            .set('Accept', 'application/json')
            .expect(200, (err, res) => {

                // Always remember to restore stubs!
                User.addUser.restore();

                expect(res.body).to.deep.equal(expectedResponse);
                done();
            });
    });

    it('Can get user from /:id route', (done) => {

        const returnedUser = {
            name: 'Steve Jobs',
            email: 'steve@gmail.com',
            password: 'some password'
        };

        const expectedResponse = {
            success: true,
            msg: returnedUser
        };

        sinon.stub(User, 'getUserById').callsArgWith(1, null, returnedUser);

        request
            .get('/some-id')
            .set('Accept', 'application/json')
            .expect(200, (err, res) => {
                User.getUserById.restore();
                expect(res.body).to.deep.equal(expectedResponse);
                done();
            });
    });

    it('Cannot get user from /:id route -- User with id does not exist', (done) => {

        const expectedResponse = {
            success: false,
            msg: 'Failed to find user'
        };

        // When there is no user, our method passes two arguments -- null for the error 
        // and null for the user 
        sinon.stub(User, 'getUserById').callsArgWith(1, null, null);

        request
            .get('/some-id')
            .set('Accept', 'application/json')
            .expect(200, (err, res) => {
                User.getUserById.restore();
                expect(res.body).to.deep.equal(expectedResponse);
                done();
            });
    });

    it('User is successfully authenticated', (done) => {
        const existingUser = {
            _id: 'some-id',
            name: 'Steve Jobs',
            email: 'steve@gmail.com',
            password: 'some password'
        };

        // Rather than stubbing the jwt.sign() method, we can just run it to determine result
        const token = jwt.sign({data: existingUser}, config.secret, {
            expiresIn: 2629746
        });

        const expectedResponse = {
            success: true,
            token: 'Bearer ' + token,
            user: {
                id: existingUser._id,
                name: existingUser.name,
                email: existingUser.email
            }
        };

        // We ensure that the user is successfully found in the database and returned through callback
        sinon.stub(User, 'getUserById').callsArgWith(1, null, existingUser);

        // We force the comparePassword() method to return a match
        sinon.stub(User, 'comparePassword').callsArgWith(2, null, true);

        request
            .post('/authenticate/some-id')
            .send(existingUser)
            .set('Accept', 'application/json')
            .expect(200, (err, res) => {

                // Always remember to restore stubs!
                User.getUserById.restore();
                User.comparePassword.restore();

                expect(res.body).to.deep.equal(expectedResponse);
                done();
            });
    });

    it('User typed in the wrong password', (done) => {
        const existingUser = {
            _id: 'some-invalid-id',
            name: 'Steve Jobs',
            email: 'steve@gmail.com',
            password: 'some password'
        };

        const expectedResponse = {
            success: false,
            msg: 'Wrong password'
        };

        // We ensure that the user is successfully found in the database and returned through callback
        sinon.stub(User, 'getUserById').callsArgWith(1, null, existingUser);

        // We force the comparePassword() method to return a mismatch (i.e. the user entered the wrong password)
        sinon.stub(User, 'comparePassword').callsArgWith(2, null, false);

        request
            .post('/authenticate/some-invalid-id')
            .send(existingUser)
            .set('Accept', 'application/json')
            .expect(200, (err, res) => {

                // Always remember to restore stubs!
                User.getUserById.restore();
                User.comparePassword.restore();

                expect(res.body).to.deep.equal(expectedResponse);
                done();
            });
    });
});
```

# Debugging SinonJS unit tests 

One of the most overlooked tools in the NodeJS toolbox is the debugger.  There are ways to use the debugger in any text editor, but I highly suggest downloading [VSCode](https://code.visualstudio.com/download) if you haven't already because not only is it a wonderful code editor, but it also has a super flexible debugging tool.  This text will not provide a comprehensive tutorial on the debugger, but will get you up and running.

First, click on the bug icon on the left side of VSCode.  This tab will open a sidebar with a Variables, Watch, Call Stack, and Breakpoint window.  You cannot use it immediately because you need to create a configuration.  Find the gear icon at the top of the Debug sidebar and select the `launch.json` file.  This file can also be found inside the `.vscode/` directory that is automatically created for each of your VSCode projects.  When you have this open, there should be a button in the bottom right corner that says "Add Configuration...".  Click this button and select the "{} Node.js: Mocha Tests" configuration.  This will add a new object to your array of configurations.  Close out of launch.json and you are now ready to debug.  Let's imagine that your test on the `User.addUser()` method is behaving unexpectedly.  Open the `user-model.js` file and find the method you want to test.  Now, add a breakpoint to this method by clicking on the line number at the left of the file window.  This will create a red dot next to that line indicating that a breakpoint has been set.  In other words, when you press the play button on the debugger, the code will run until it reaches that specific line.  Once it reaches your breakpoint line, you will be able to step into the code and determine what variables are being set, which functions are being called, etc.  Again, your `launch.json` file should look similar to this: 

```javascript 
{
    "type": "node",
    "request": "launch",
    "name": "Mocha Tests",
    "program": "${workspaceFolder}/node_modules/mocha/bin/_mocha",
    "args": [
        "-u",
        "tdd",
        "--timeout",
        "999999",
        "--colors",
        "${workspaceFolder}/test"
    ],
    "internalConsoleOptions": "openOnSessionStart",
    "skipFiles": [
        "<node_internals>/**/*.js",
        "${workspaceFolder}/node_modules/**/*.js"
    ]
}
```

If you created your launch.json using the "Add Configuration..." button, you will not see the `skipFiles` line.  This is because I have manually inserted this line to prevent the debugger from debugging native NodeJS code.  The `"<node_internals>/**/*.js"` is a pattern provided by the [VSCode docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_skipping-uninteresting-code-node-chrome).  The `"${workspaceFolder}/node_modules/**/*.js"` line tells the debugger to skip through all of the code run by the packages in your `node_modules/` directory.  In other words, it skips through the Mocha internal functions.  The net effect of all this is to allow you to focus only on _your_ code.

You may experience that the debugger initially skips over all of your code at the beginning when you have skipped certain files.  Just keep clicking the "step into" button and you will eventually hit the code that you personally wrote.  Feel free to tweak your `launch.json` file for more custom debugging functionality.

Debugging is kind of like testing.  It's one of those things that a new developer will never be excited to learn, yet it can save that same developer more time than he could ever imagine if he were to just spend a few minutes learning this critical skill.

# Conclusion

As mentioned, testing and debugging are two skills in the toolbox of a developer that are often overlooked and/or avoided.  Despite this, going through the pain of teaching yourself these two skills will not just enhance your effectiveness as a developer, but will transform your way of development completely.  No longer will you spend countless hours writing `console.log()` statements trying to figure out where things are going wrong.  No longer will you add a feature to your project, walk around the house all proud for a few hours, and then find that you're going to be up late that night because that awesome feature actually broke the rest of your code, and your only debugging technique is "console.logging".  You will instead write a feature knowing that if it breaks something else, your test suite will tell you, and furthermore, you will easily be able to debug it.

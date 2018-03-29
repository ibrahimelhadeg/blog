---
title: Async/Await, Webpack, Babel and the Regenerator Runtime Error Tutorial
date: 2018-03-03 10:27:51
tags: javascript
---

For anyone struggling to compile `async/await` code with webpack and babel, I wanted to put together this short tutorial that should clear the air on this topic.

## A Short Example

To understand what is going on, let's replicate this error in a simple project. Most likely you have stumbled upon the Regenerator Runtime error in a larger project, but it is no use to try and debug a hundred files all at once.

So let's create a new project:

```
mkdir async-webpack-babel
cd async-webpack-babel
touch package.json
```

Now place the following code in your `package.json`

```json
{
  "name": "webpack-babel-tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "webpack",
    "start": "webpack-dev-server --output-public-path=/build/",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.3",
    "babel-preset-env": "^1.6.1",
    "webpack": "^3.8.1",
    "webpack-dev-server": "^2.9.5"
  }
}
```

I have included this `package.json` so that we are all working with the same versions of webpack and babel.

Now, let's create some files.

```
touch index.html webpack.config.js
mkdir src
touch src/app.js
```

Here are what each file should contain:

```javascript
// webpack.config.js

const path = require("path");

module.exports = {
  entry: {
    app: "./src/app.js"
  },
  output: {
    path: path.resolve(__dirname, "build"),
    filename: "app.bundle.js"
  },
  module: {
    loaders: [
      {
        test: /\.js?$/,
        exclude: /node_modules/,
        loader: "babel-loader",
        query: {
          presets: ["env"]
        }
      }
    ]
  }
};
```

```javascript
// src/app.js

async function getPosts() {
  const response = await fetch("https://jsonplaceholder.typicode.com/posts/1");

  const data = await response.json();

  return data;

  getPosts().then(posts => console.log(posts));
}
```

Let's do a bit of a recap here. At this moment, we are using our `webpack.config.js` and the command `npm run build` to compile all of our javascript to ES5 browser compatible code. This is then output into `app.bundle.js` which is imported into `index.html`. If you run `npm run start`, everything will compile okay, but in the browser, you should have an error in the console that reads something like

```
app.bundle.js:74 Uncaught ReferenceError: regeneratorRuntime is not defined
    at app.bundle.js:74
    at Object.defineProperty.value (app.bundle.js:103)
    at __webpack_require__ (app.bundle.js:20)
    at app.bundle.js:63
    at app.bundle.js:66
```

## Solving the Problem

The problem is caused because `async/await` is an ES7 feature, and our webpack and babel config are not able to recognize this.

To solve the problem, we are going to need to download a few more packages and update the `webpack.config.js` file.

```
npm install --save-dev babel-polyfill babel-preset-stage-0
```

Now, update the `entry` part of `webpack.config.js` to reflect the following:

```javascript
entry: {
  app: ["babel-polyfill", "./src/app.js"];
}
```

And finally, update the `query` field to:

```javascript
query: {
  presets: ["env", "stage-0"];
}
```

Run `npm start` and the error is gone! Hope this solves the issue for anyone struggling with this. If you are still getting an error, make sure you are running the same versions as shown in `package.json`.

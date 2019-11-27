---
title: How to Debug NodeJS Code in VSCode
tags:
    - debugging
---

When I first learned to code, the last thing on my mind was debugging, despite the hundreds of inevitable mistakes I would make in the next few years.  Like [unit testing](/blog/2019/express-sinon), developing good debugging skills is a must for any programmer.

Thanks to all the dependencies and modules in a single NodeJS project, it is often intimidating to whip out the debugger and start walking through what seems like an infinite loop of functions.

In this post, I will show you how to use Visual Studio Code IDE to debug your projects without too much of a hassle.  There is a slight learning curve, but after a few reps, you'll get the hang of it.  We will walk through each of the following topics: 

* Debugging a simple Javascript file
* Debugging an Express JS application
* Debugging a front-end application with Google Chrome and VSCode

As we move through the tutorial, the debugging techniques will gradually increase in difficulty.  Many tutorials dive right in to debugging a complex application which dramatically increases the difficulty of understanding the VSCode debugging tool in the first place!

## Debugging a Simple Javascript File in VSCode

I have created a simple file with no dependencies below:

```javascript
function simpleFunction(arg1, arg2) {
    
    if (typeof arg1 === 'number' && typeof arg2 === 'number') {
    
        return {
            operation: "multiplication",
            product: arg1 * arg2
        };
    
    } else {
        
        return {
            operation: "concatenation",
            result: arg1 + " " + arg2
        }
    
    }

}

setTimeout(() => {

    console.log(simpleFunction("hello", 8));

}, 3000);
```

This file doesn't do anything useful, but it is as simple as it gets, and therefore a great place to start with debugging.

Below are the keyboard shortcuts for using the VSCode debugger.  As you go through this first file, start getting used to these shortcuts as they will be invaluable moving forward.

| Command        | Shortcut          |
|----------------|-------------------|
| Continue/Pause | F5                |
| Step Over      | F10               |
| Step Into      | F11               |
| Step Out       | Shift + F11       |
| Restart        | Ctrl + Shift + F5 |
| Stop           | Shift + F5        |

While in this file, type `F5`.  This will automatically start the debugger, but you will notice that the file completes execution after 3 seconds and the debugger stops.

If we wanted to see what is going on with the code, we need to place a breakpoint on it.  The easiest way to do this is by clicking in the left margin:

{% asset_img debug-1.gif %}

Once you have added a breakpoint, you can step through your code using `F10`.  As you step through your code, you will see the your variables and call stack on the left-hand window pane.


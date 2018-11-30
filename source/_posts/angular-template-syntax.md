---
title: Understanding Angular2+ Template Syntax
date: 2018-11-20 05:53:21
tags: Angular, Coding
---

[Link to documentation on this subject](https://angular.io/guide/template-syntax#binding-syntax-an-overview)

_Note: I will refer to all versions of Angular2+ as Angular_

```html
<img [src]="imageLinkProperty"> <!-- #1 -->
<app-header [headerTitle]="titleProperty"></app-header> <!-- #2 -->
<div [ngClass]="{'special': isSpecial}"></div> <!-- #3 -->
<button (click)="onClickFunction()">Click Me</button> <!-- #4 -->
<div (customClickDirective)="clicked=$event"></div> <!-- #5 -->
<input [(ngModel)]="someInputProperty"> <!-- #6 -->
<div [class.special]="someClassProperty">This is styled based on someClassProperty</div> <!-- #7 -->
<button [style.color]="someStyleProperty ? 'red' : 'green'">This button is conditionally styled</button> <!-- #8 -->
<p *ngIf="somePropIsTrue"></p> <!-- #9 -->
<form #myForm="ngForm"> <!-- #10 -->
```

Above is a non-exhaustive list of _different_ ways that you can bind properties and data in Angular.  Daunting? Yep.  Powerful?  Yep.  This short post will walk through each of the possibilities.

## 1 - Element Property 

```html
<img [src]="imageLinkProperty"> 
```

To properly understand this, we need to understand the [difference between an html attribute and property](https://stackoverflow.com/questions/6003819/what-is-the-difference-between-properties-and-attributes-in-html).  In short, an attribute pertains to the actual html element while a property pertains to the property of the DOM element that was created to display in the browser.  The trick is that there are some attributes that don't have DOM properties and vice-versa, so Angular only binds to _non-reflective_ properties.  A _non-reflective_ property means that the property of the DOM element does not _reflect_ the attribute of the html element.  Take the following example.

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'my-app',
  template: '<input [id]="myElemId" [value]="myElemValue"><button (click)="change()">Click to Change</button>',
})
export class AppComponent {
  myElemValue = 'initial value';
  myElemId = 'initialId';

  change() {
      this.myElemValue = 'new value';
      this.myElemId = 'newId';
  }
}
```

Above, we have a super simple Angular component with a single input and button as HTML for the component.  When clicked, the button will change the values of the `id` attribute and `value` attribute.  Before we click the button, we can see the element in the console.

```javascript 
let myElem = document.getElementById("initialId");
myElem.value // initial value 
myElem.id // initialId
myElem.getAttribute('value') // initial value 
myElem.getAttribute('id') // initialId
```

Nothing unexpected.  Now, let's click that button to change the attributes and properties.  After clicking, we go to the console again.

```javascript
let myElem = document.getElementById("newId");
myElem.value // newValue
myElem.id // newId
myElem.getAttribute('value') // null
myElem.getAttribute('id') // newId
```

The properties of the element have changed and the `id` attribute has changed, but the `value` attribute has not.  This is because it is a _non-reflective_ property.  This is why in some cases, we can bind to HTML properties (`src`) and sometimes we cannot (`value`).  Going back to our first example, every time the value of `imageLinkProperty` changes, the image will change in the HTML.

## 2 - Component Property 

```typescript
// File: app.component.html

<app-header [headerTitle]="titleProperty"></app-header>
```

This is a bit simpler than the first example.  Here, we have two components.

```typescript 
// File: header.component.ts 

import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-header',
  template: '<p>{{ headerTitle }}</p>',
})
export class HeaderComponent {
    @Input() headerTitle: string;
}
```

```typescript
// File: app.component.ts 

import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: '<app-header [headerTitle]="titleProperty"></app-header>',
})
export class AppComponent {
  titleProperty: string = 'some title';
}
```

In this example, we have two components--a parent component `app-root` and a child component `app-header`.  We want to make some data from the parent component available for use in the child component and we do this through component property binding.  The app root will define the `titleProperty` and bind it to the `headerTitle` property that exists as an `Input()` in the `app-header` (child component).  Consuming it as an input, the child component can use `headerTitle` however it wants!

## 3 Directive Property

```html 
<div [ngClass]="{'special': isSpecial}"></div>
```

This works very similar to the previous example but instead of making a property available for a child component, we are making a property (or value in this case) available for a directive.  We don't care about what the directive does.  All we care about is that we are giving it the correct data to consume and do its magic.  In this case, we are providing the value `{'special': isSpecial}` to Angular's built in `ngClass` directive.  This directive will use that data and conditionally apply classes to the element.

## 4 Element Events 

```html 
<button (click)="onClickFunction()">Click Me</button>
```

In my opinion, element events are the simplest trick that Angular has.  You simply define the DOM event that you want to wait for on that particular element and define a function to call when the event is clicked. 

## 5 Custom Events 

```html 
<div (customClickDirective)="clicked=$event" clickable>{{ clicked }}</div> <!-- #5 -->
```

You can define custom events in either components (as we saw with the input in #2) or directives (shown here).  Let's first define a super simple directive.

```typescript 
import { Directive, Output, EventEmitter, ElementRef } from '@angular/core';

@Directive({
  // This is the selector that we will call in the html template 
  // call it like so:  (customClickDirective)=""
  selector: '[customClickDirective]',
})
export class CustomClickDirective {
    
    // customClickDirective is the alias that the component consuming the output will use to call it
    @Output('customClickDirective') clicks = new EventEmitter<string>(); 

    counter = 0;

    // ElementRef passes the element this directive sits on to the Directive Class
    constructor(el: ElementRef) {

        // Add an event listener to the current element
        el.nativeElement.addEventListener('click', (event: Event) => {

            // Every time the Directive detects a click on the element it increments the counter
            // and emits a string with the counter value in it
            this.clicks.emit(`Counter: ${this.counter++}`);
        });
    }
}
```

## 6 Two Way Data Binding 

```html
<input [(ngModel)]="someInputProperty">
```

This is a simple yet powerful feature of Angular.  In a component, we can create a data stream between an HTML element's value and a property of the component.  Every time this HTML input changes in value, the `someInputProperty` which exists in the component will change.  You must use `ngModel` directive to achieve this.

## 7 and 8 Styles 

```html
<div [class.special]="someClassProperty">This is styled based on someClassProperty</div> <!-- #7 -->
<button [style.color]="someStyleProperty ? 'red' : 'green'">This button is conditionally styled</button> <!-- #8 -->
```

Although it makes more sense to use the `ngClass` or `ngStyle` built in directives usually, you can also edit classes and styles directly.

## 9 Structural Directives

```html
<p *ngIf="somePropIsTrue"></p>
```

A structural directive is a directive that can alter any part of the DOM and not just the element that it sits in.  It is called with an asterisk and can most commonly be seen in the form of `*ngIf` or `*ngFor`.  This is opposed to an _attribute directive_ which only affects the element it sits on (ex: `ngClass`).

## 10 References

```html
<form #myForm="ngForm">
```

You can get a reference to anything in a template using the hashtag syntax.  By setting this variable on a form, we can retrieve the values of a form from anywhere.  A simpler way of using this is seen below.

```html 
<input #phonenumber placeholder="111-111-1111" value="111-111-1111">
<p>The current phone number is {{ phonenumber.value }}</p>
```

# Conclusion 

Although not exhaustive, this list covers most of the basics of Angular templating.  Although it is complex to learn due to the seemingly infinite number of possibilities, this is the power of Angular and should be learned thoroughly.
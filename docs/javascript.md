# JavaScript

JavaScript is used in our Customer Web App, on certain interactive pages in
Liefery Core (Liefery Backend) and in the Whitelabel Tracking application.

## General

* Write `use strict` at the top of every js file
* Whitelabel tracking is written with React, Redux, ES6 and Flow for types. Please
  refer to the [React style guide](react_style_guide.md)
* Liefery Core and the Customer Web App are currently written using the Angular
  framework, and [CoffeeScript](http://coffeescript.org/), a language that compiles
  to JavaScript.
    * Overview of best practices for Angular can be found [here](https://github.com/liefery/liefery-backend/wiki/Liefery-Conventions-and-Best-Practices#js--angular)
    * Comprehensive Angular Style guide can be found [here](https://github.com/johnpapa/angular-styleguide/tree/master/a1).

## Testing

* All JavaScript code should have a corresponding unit test where possible.
* We use the [Jasmine](https://jasmine.github.io/2.0/introduction.html) testing
framework for these tests and aim for a clean BDD style of test description.

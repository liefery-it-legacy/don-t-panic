# Flow in Whitelabel app

[Flow](https://flow.org/en/docs/) is a static type-checker for JavaScript. You can run it locally via `$ yarn flow`.

Static type-checking serves the same purpose as certain unit tests. Through warnings directly in your text editor, it can guard you from mistakes like accessing properties on `null`, or calling an object as though it were a function. The ideal is to catch all runtime errors before the code even runs.

## Basic guidelines

In practice, statically typing a dynamic language like JavaScript isn't always such a straightforward task.  In particular, external libraries call for some extra steps (see Gotchas below). Luckily, only those files starting with a `// @flow` comment will go through type-checking, so we aren't locked in when it's inconvenient. From within already-typed code, we can avoid giving a value a precise type via `any` or `*`, **though this should be a last resort**.

One place where typing your JavaScript _is_ really straightforward is in reducer and action modules. Since these consist mostly of pure functions with few external dependencies, their type definitions involve mostly just JavaScript primitives and the simplest object types. For reducers and action functions, we aim to provide type definitions for both their parameters and return values.

## Gotchas

### Exact types
Use our 'Exact<YourObjectType>' type instead of Flow's native [exact types](https://flow.org/en/docs/types/objects/#toc-exact-object-types), which are
  [broken with `Object.assign` and the object spread operator](https://github.com/facebook/flow/issues/2405).

### Using Flow with external modules

If you're importing an external module in a Flow-typed file,
it has to have a [module definition](https://flow.org/en/docs/libdefs/creation/) in `app/javascript/flow`.
Feel free to put one there yourself if you need to!

Ideally, your module will have an entry in the [`flow-typed`](https://github.com/flowtype/flow-typed/tree/master/definitions/npm) repository, which you can
just copy and paste into a new file in `app/javascript/flow`. (This is the same thing done
by the `flow-typed` CLI--it's not ideal but it's the best system we've got!) If it's not in
`flow-typed`, you can probably get away with declaring your export type as `any`.

```js
// @flow
declare module 'my-obscure-library' {
  declare var exports: any;
}
```

You may then proceed to provide more specific type information as you see fit. Say you're importing the named export `parse` from `'my-obscure-library'`, which takes a string and returns an object:

```js
// @flow
declare module 'my-obscure-library' {
  declare var exports: {
    parse: (string) => Object,
  };
}
```

## Summary

* *Always* type your reducer and action modules. Don't forget return types!
* Typing is nice to have in other modules.
* Beware of native Flow exact types! Use our `Exact`.

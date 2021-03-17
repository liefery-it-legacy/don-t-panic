Redux
---

We use Redux to manage our app state. The [official docs](https://redux.js.org/) are really good! As well as author [Dan Abramov's videos on Egghead](https://egghead.io/courses/getting-started-with-redux).

## Basic terminology

* **store** - A plain JavaScript object with methods like `getState()` to get the current state tree, and `dispatch()` to update the state tree.
* **action** - A small, plain JavaScript object we send to `dispatch()` in order to specify what kind of update to perform on our state tree.
* **action creator** - A plain JavaScript function that returns an action. They save us from having to type out long object literals.
* **reducer** - A plain JavaScript function where we define *how to update the state tree in response to any given action*. It's this function that you pass to Redux's `createStore()` function to get a working store.
* **middleware** - An optional argument to `createStore` for extending the functionality of `dispatch()`. We use `redux-thunk` middleware to more easily dispatch actions both *conditionally* and *asynchronously*.

## Flow and Tests

[Flow](flow.md) does the same jobs as certain unit tests--specifically, those tests that
are simple checks about functions' *input* and *output*. For this reason, we eschew action creator tests, as well the most basic reducer tests, in favor of type definitions.

## Testing thunks

A thunk is a function we pass to `dispatch()` instead of a plain action object. Inside a thunk, we have access to `dispatch()` and `getState()` as function parameters. This lets us query the state tree and do asynchronous operations (like Ajax requests) *before ultimately deciding which actions to dispatch*.

Therefore, the job of a thunk test is **to verify that the right actions were dispatched** in response to either or both of the following:
 1. the current app state
 2. the result of asynchronous operations like Ajax requests.

That's it--the eventual effects of those actions on the state tree should already be tested in reducer tests, and the shape of actions created by action creators is kept in check by type definitions.

### Mocking the current app state

We use `redux-mock-store` for testing thunks. We can provide a `mockStore()` with a stub version of the current app state. Just like a normal Redux store, this mock store has a `dispatch()` method. The big difference is that after we `dispatch()` our thunk, the mock store can tell us all the actions that were subsequently dispatched by our thunk via `getActions()`.

### Mocking Ajax requests

We are using [Nock](https://github.com/node-nock/nock) to mock Ajax requests.

React style guide
===

If you are just getting started with React, please refer to the very well written documentation for
[React](https://facebook.github.io/react/docs/thinking-in-react.html ) and
[Redux](http://redux.js.org/docs/basics/)

## React
### File structure
  React components can be found in `app/javascript/components`.

    components/
    ├── Header/
    │   ├── index.js
    │   ├── index.test.js
    │   ├── Header.css

  Within each component folder is the component itself  `index.js`, the tests `index.test.js`
  and the required `.css` files `Header.css`.

### Unit tests
We use [Jest](https://facebook.github.io/jest/docs/en/getting-started.html) as our unit testing tool.

### Enzyme

[Enzyme](http://airbnb.io/enzyme/docs/api/shallow.html) is a React testing utility library. We use it for testing the output that components render as well as the wiring up of component events.

#### Testing component output

The basic steps for testing rendered output are as follows:

1. Pass your component to [`shallow()`]((https://github.com/airbnb/enzyme/blob/master/docs/api/shallow.md)) to get your enzyme wrapper.
  ```js
  import React from 'react' // has to be in scope when we're writing JSX
  import { shallow } from 'enzyme'

  describe('FormField', () => {
    // this vague description is just for demo purposes!
    it('renders correct output', () => {
      const wrapper = shallow(<FormField />)
      // continued below
    })
  })

  ```
2. Give your component all required props as normal. If you are using Component state, you can use `setState()`.
  ```js
  // inside your 'it' block
  const wrapper = (<FormField labelText="Name" />)
  wrapper.setState({ errorText: 'This field cannot be blank' })
  ```
3. Test the output is correct using [methods on the wrapper](https://github.com/airbnb/enzyme/blob/master/docs/api/shallow.md). You can do things like query rendered text with `text()`, and get wrappers for child elements/components with `find()`.
  ```js
  // still inside your 'it' block
  expect(wrapper.text()).toContain('Name')

  // in real life, you'd put additional
  // expectations in their own 'it' blocks
  const inputElement = wrapper.find('input')
  expect(inputElement.prop('disabled')).toBe(true)

  const errorMessageComponent = wrapper.find('ErrorMessage')
  expect(errorMessageComponent.prop('text')).toBe('This field cannot be blank')
  ```

#### Testing component events

1. If the behavior under test involves [lifecycle methods](https://reactjs.org/docs/react-component.html#the-component-lifecycle) or complex DOM interaction, pass your component to [`mount()`](https://github.com/airbnb/enzyme/blob/master/docs/api/shallow.md). (The more lightweight `shallow()` is preferred wherever it's usable.)
  ```js
  import React from 'react'
  import { mount } from 'enzyme'

  describe('FormField', () => {
     it('reacts to events', () => {
       const wrapper = mount(<FormField />)
       // continued below
     })
  })
  ```
2. Replace your component's function props with [mock functions](https://facebook.github.io/jest/docs/en/mock-functions.html).
```js
// inside 'it' block
const onFocus = jest.fn()
const updateFieldText = jest.fn()
const wrapper = mount(<FormField onFocus={onFocus} updateFieldText={updateFieldText} />)
```
3. Simulate events with methods like `click()`, and trigger lifecycle events with methods like `setProps()`, `setState()`, `unmount()`.
```js
const input = wrapper.find('input')
input.click()
wrapper.setState({ text: ' NonNormalizedInput   ' })
```
4. Test that your function props were called with the [appropriate Jest expectations](https://facebook.github.io/jest/docs/en/expect.html#tohavebeencalled).
```js
expect(onFocus).toHaveBeenCalled()
expect(updateFieldText).toHaveBeenCalledWith('nonnormalizedinput')
```

### `setup()` functions

You'll notice in our Enzyme tests, we like to use `setup()` functions instead of the `before`/`after` hooks you might see in tutorials.

This is a way to reduce duplication in tests. Especially when a component has lots of props, leaving some of them out might trigger propType warnings or break the component entirely, even if they're entirely irrelevant to the current tests. You can avoid this problem by calling `shallow()` or `mount()` inside of a `setup()` function, and putting all required props in there.

### Connected Components

A component that takes props directly from the [Redux store](#basic-terminology) via `react-redux`'s `connect()` is called a *container* or a *connected component*.

They usually look something like this:

```javascript
import React from 'react'
import { connect } from 'react-redux'

class Header extends Component {
  render() {
    return (
      <header>
        <img src={this.props.logo} />
      </header>
    )
  }
}
const mapStateToProps = state => ({
  logo: state.logoImageURL
})
export default connect(mapStateToProps)(Header)
```

Connected components work because we always instantiate them from within the special `Provider` component from `react-redux`. It's this `Provider` that actually connects `Header` to the Redux store. Since we give `Provider` the store as a prop, it can call `mapStateToProps` when the time comes and make `this.props.logo` available for us to use within `render`.

#### Testing Connected Components

When we're writing unit tests, it would be a giant hassle to have to wrap every instance of `Header` inside a `Provider`, and to pass an entire Redux store to each `Provider`. That's why we **don't test the connected component itself**, but rather test the Component *prior* to passing it to `connect()`.

```javascript
// add export keyword here
export class Header extends Component {
// ...
// ...
}
export default connect(mapStateToProps, mapDispatchToProps)(header)
```

This allows our test file to import the unwrapped class:

 ```js
 import { Header } from './Header'

 describe('Header', () => {
   it('renders a logo image', () => {
     // ...
   })
 })
 ```

And our wrapped component can continue to be used in the application as normal!

Note that we need to disable the linter rule (`import/no-named-as-default`)[https://github.com/benmosher/eslint-plugin-import/issues/544] when we import the default export.

```js
import ZipEntry from './ZipEntry' // eslint-disable-line import/no-named-as-default
```

## CSS

#### Linting

We use `stylelint` to enforce a consistent style and best practices (such as the use of classnames in selectors over IDs and element names). You can run it locally with `$ yarn stylelint`.

#### Preprocessing

We use Sass. This is set up by default in Webpacker. We don't distinguish between SCSS and CSS files via extensions--use Sass syntax freely in `.css` files.

#### CSS Modules
We write our styles in [modules](https://github.com/css-modules/css-modules) in order to avoid the problems associated with CSS's global namespace. This eliminates the hassle of complex naming schemes like BEM or SMACSS.

The way this works is that we put all styles associated with a component `MyComponent` inside a file `MyComponent.css` like this:

```css
.container {
  position: relative;
}

.input {
  border: none;
}
```

...and Webpack will transform those classnames in such a way that automatically prevents naming conflicts.

```html
// final rendered HTML
<section class="MyComponent__container--xkCd42">
  <input class="MyComponent__input--dLi60M" type="text">
</section>
```

This requires us to import the classnames like this from our Component code:

```js
// MyComponent.js
import { container, input } from './MyComponent.css'

const MyComponent = () => (
  <section className={container}>
    <input className={input} type="text" />
  </section>
)

export default MyComponent
```

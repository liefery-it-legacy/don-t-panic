# Angular

## General

* We generally follow the [Todd Moto guide](https://github.com/toddmotto/angular-styleguide)
  * Prefer Angular Components over Directives
  * Embrace [unidirectional dataflow](https://toddmotto.com/one-way-data-binding-in-angular-1-5/)
  * Components, filters and services should live in `components`, `filters`, and `services` folders, respectively. These are nested under the folder named after the feature.
  * Component's definition and controller belong to separate files and are named `component_name.component.js` `component_name.controller.js`, respectively.
  * Component folder structure is always flat (do not nest components inside of component folders, even if the component is nested within that in the DOM)
  * Folder Structure:

  ```
  app/assets/javascripts/
  |- module_name/
  |- |- components
  |- |- |- component_1.controller.js
  |- |- |- component_1.component.js
  |- |- |- component_2.controller.js
  |- |- |- component_2.component.js
  |- |- services
  |- |- filters
  ```
* **Injection** should be done in the following order:
  * Angular dependencies (like `$element`, `$q`, `$window`) first
  * Third party dependencies second
  * app dependencies last
  * generally alphabetical order (in the groups)

* Components can hold their own **state**, as long as it is not shared with other components
  * **Shared state** should always go to the component that is the nearest shared parent component in the component tree

## Controller

* Avoid `$scope` unless you need to update something from a non-Angular event (i.e. from a google event, `$scope` apply)
* Bind callback methods to `this`
  ```js
  class SubmitButtonController {
    static $inject = ['RainService']

    constructor(RainService) {
      this.rainService = RainService
    }

    handleClick = () => {
      this.rainService.stopIt().then(() => {
        this.rideIntoSunset()
      })
    }

    rideIntoSunset() {
      …
    }
  }

  angular
    .module('app.core')
    .controller('SubmitButtonController', SubmitButtonController)
  ```

## Component definition

* Set `controllerAs` to `ctrl` for every component.  Example component:

  ```js
  const submitButton = {
    controller: 'SubmitButtonContrller',
    controllerAs: 'ctrl',
    bindings: {
      onSubmit: '<',
    },
    template: "<button on-click='ctrl.handleClick'>Click Me</button>",
  }

  angular
    .module('app.core')
    .component('submitButton', submitButton)
  ```

  * Template
  If a template consists of only a couple of lines, keep it in the same file. If it's longer, extract it to its own HTML file and use `require` to load it:
  ```js
  const template = require('submit_button.template.html')
  
  const submitButton = {
    controller: 'SubmitButtonContrller',
    template
  }  
  ```

* Bindings
  * use them to define the interface with other components
  * avoid `=` and `&` bindings, use `@` to pass raw text and `<` for anything else(see http://blog.krawaller.se/posts/dissecting-bindings-in-angularjs/)
  * always list callbacks last
  * **careful:** HTML attributes that denote bindings are written with `-` (kebab-case) and Angular interprets this as CamelCase
  * callbacks are passed down via HTML attribute
  ```html
    <submit-button on-submit="ctrl.parentSubmit" />
  ```

## Services

* Keep logic in the services

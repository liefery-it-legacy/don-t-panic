CSS style guide
===


## CSS
### File structure
  CSS components are grouped inside a `components/` directory. For [liefery-backend](https://github.com/liefery/liefery-backend) this would be `app/assets/stylesheets/components`.

    components/
    ├── status_tag.scss
    ├── time_to_next_action.scss
    ├── top_navigation.scss
    ├── ...

### CSS structure
We follow the spirit of BEM convention to modularise our CSS. You can find more information here: http://getbem.com/introduction/

This is how it looks like:

```css
.Block {}

.Block__element {}

.Block--modifier {}

.Block__element--modifier {}
```

More concrete:

```css
.TopNavigation {
  padding: 30px;
  background-color: #E64A19;
}

.TopNavigation__link {
  background-color: #FFFFFF;
}

.TopNavigation--slim {
  padding: 10px;
}

.TopNavigation__link--cta {
  background: #FFFFFF;
  color: #E64A19;
}
```

```html
// HTML
<div class="TopNavigation">
  <a href="#" class="TopNavigation__link">Link 1</a>
  <a href="#" class="TopNavigation__link">Link 2</a>
  <a href="#" class="TopNavigation__link--cta">Link CTA</a>
</div>
```

For [whitelabel-tracking](https://github.com/liefery/whitelabel-tracking) and [homepage](https://github.com/liefery/homepage) we write our styles in [modules](https://github.com/css-modules/css-modules). This automatically generates BEM conform css structure.

### Property ordering

We order properties according to an "outside-in" logic.

In this system, highest priority goes to those properties that affect the positions and dimensions of elements **outside** the selected element. For instance, an element with `display: none` is taken entirely out of the document flow--so `display` goes at the very top. Properties that determine the _dimensions_ of the selected element, like `width` and `height`, come soon afterwards, since they have the power to push other elements around. Other properties that belong to this category of "dimensional" properties include `margin`, `border`, and `padding`--we place them in that exact order, in keeping with that "outside-in" principle.

```css
.Container {
  // selected element's positioning and dimensions
  display: flex;
  position: relative;
  padding: 1em;

 // children elements' positioning and dimensions
  flex-direction: column;
  justify-content: space-between;
  align-items: center;

  // properties with minimal effect on positioning and dimensions
  // (mostly typography and colors)
  text-align: center;
  font-weight: $weight-thin;
  color: $color-text-contrast;
}
```

### Linting

We use `stylelint` to enforce a consistent style and best practices (such as the use of classnames in selectors over IDs and element names).

### CSS classes for testing

We want to distinguish between css classes for styling and css classes for testing. A feature spec should never rely on a class that we use for styling. For new rspec feature specs
we prefix the classes so it is clear that this class is used for testing. For legacy cucumber tests we keep the smacss-ish css classes.

```html
<div class="TopNavigation">
  <a href="#" class="TopNavigation__link">Link 1</a>
  <a href="#" class="TopNavigation__link--cta test--create-shipment-link">Link CTA</a>
</div>
```

Within a page object:

```ruby
def go_to_new_shipment_page
  find(".test--create-shipment-link").click
end
```

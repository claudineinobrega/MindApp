# MindApp

## Cloud Tags with Ruby and Ruby on Rails framework


## Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Installation](#installation)
- [Usage](#usage)
- [Custom matchers](#custom-matchers)
  - [`toBeDisabled`](#tobedisabled)
  - [`toBeEnabled`](#tobeenabled)
  - [`toBeEmpty`](#tobeempty)
  - [`toBeInTheDocument`](#tobeinthedocument)
  - [`toBeInvalid`](#tobeinvalid)
  - [`toBeRequired`](#toberequired)
  - [`toBeValid`](#tobevalid)
  - [`toBeVisible`](#tobevisible)
  - [`toContainElement`](#tocontainelement)
  - [`toContainHTML`](#tocontainhtml)
  - [`toHaveAttribute`](#tohaveattribute)
  - [`toHaveClass`](#tohaveclass)
  - [`toHaveFocus`](#tohavefocus)
  - [`toHaveFormValues`](#tohaveformvalues)
  - [`toHaveStyle`](#tohavestyle)
  - [`toHaveTextContent`](#tohavetextcontent)
  - [`toHaveValue`](#tohavevalue)
  - [`toHaveDisplayValue`](#tohavedisplayvalue)
  - [`toBeChecked`](#tobechecked)
  - [`toHaveDescription`](#tohavedescription)
- [Deprecated matchers](#deprecated-matchers)
  - [`toBeInTheDOM`](#tobeinthedom)
- [Inspiration](#inspiration)
- [Other Solutions](#other-solutions)
- [Guiding Principles](#guiding-principles)
- [Contributors](#contributors)
- [LICENSE](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Installation

This module is distributed via [npm][npm] which is bundled with [node][node] and
should be installed as one of your project's `devDependencies`:

```
npm install --save-dev @testing-library/jest-dom
```

or

for installation with [yarn](https://yarnpkg.com/) package manager.

```
yarn add --dev @testing-library/jest-dom
```

> Note: We also recommend installing the jest-dom eslint plugin which provides
> auto-fixable lint rules that prevent false positive tests and improve test
> readability by ensuring you are using the right matchers in your tests. More
> details can be found at
> [eslint-plugin-jest-dom](https://github.com/testing-library/eslint-plugin-jest-dom).

## Usage

Import `@testing-library/jest-dom` once (for instance in your [tests setup
file][]) and you're good to go:

[tests setup file]:
  https://jestjs.io/docs/en/configuration.html#setupfilesafterenv-array

```javascript
import '@testing-library/jest-dom'
```

> Note: If you're using TypeScript, make sure your setup file is a `.ts` and not
> a `.js` to include the necessary types.

Alternatively, you can selectively import only the matchers you intend to use,
and extend jest's `expect` yourself:

```javascript
import {
  toBeInTheDocument,
  toHaveClass,
} from '@testing-library/jest-dom/matchers'

expect.extend({toBeInTheDocument, toHaveClass})
```

> Note: when using TypeScript, this way of importing matchers won't provide the
> necessary type definitions. More on this
> [here](https://github.com/testing-library/jest-dom/pull/11#issuecomment-387817459).

## Custom matchers

`@testing-library/jest-dom` can work with any library or framework that returns
DOM elements from queries. The custom matcher examples below are written using
matchers from `@testing-library`'s suite of libraries (e.g. `getByTestId`,
`queryByTestId`, `getByText`, etc.)

### `toBeDisabled`

```typescript
toBeDisabled()
```

This allows you to check whether an element is disabled from the user's
perspective.

It matches if the element is a form control and the `disabled` attribute is
specified on this element or the element is a descendant of a form element with
a `disabled` attribute.

According to the specification, the following elements can be
[actually disabled](https://html.spec.whatwg.org/multipage/semantics-other.html#disabled-elements):
`button`, `input`, `select`, `textarea`, `optgroup`, `option`, `fieldset`.

#### Examples

```html
<button data-testid="button" type="submit" disabled>submit</button>
<fieldset disabled><input type="text" data-testid="input" /></fieldset>
<a href="..." disabled>link</a>
```

```javascript
expect(getByTestId('button')).toBeDisabled()
expect(getByTestId('input')).toBeDisabled()
expect(getByText('link')).not.toBeDisabled()
```

<hr />

### `toBeEnabled`

```typescript
toBeEnabled()
```

This allows you to check whether an element is not disabled from the user's
perspective.

It works like `not.toBeDisabled()`. Use this matcher to avoid double negation in
your tests.

<hr />

### `toBeEmpty`

```typescript
toBeEmpty()
```

This allows you to assert whether an element has content or not.

#### Examples

```html
<span data-testid="not-empty"><span data-testid="empty"></span></span>
```

```javascript
expect(getByTestId('empty')).toBeEmpty()
expect(getByTestId('not-empty')).not.toBeEmpty()
```

<hr />

### `toBeInTheDocument`

```typescript
toBeInTheDocument()
```

This allows you to assert whether an element is present in the document or not.

#### Examples

```html
<span data-testid="html-element"><span>Html Element</span></span>
<svg data-testid="svg-element"></svg>
```

```javascript
expect(
  queryByTestId(document.documentElement, 'html-element'),
).toBeInTheDocument()
expect(
  queryByTestId(document.documentElement, 'svg-element'),
).toBeInTheDocument()
expect(
  queryByTestId(document.documentElement, 'does-not-exist'),
).not.toBeInTheDocument()
```

> Note: This matcher does not find detached elements. The element must be added
> to the document to be found by toBeInTheDocument. If you desire to search in a
> detached element please use: [`toContainElement`](#tocontainelement)

<hr />

### `toBeInvalid`

```typescript
toBeInvalid()
```

This allows you to check if a form element, or the entire `form`, is currently
invalid.

An `input`, `select`, `textarea`, or `form` element is invalid if it has an
[`aria-invalid` attribute](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-invalid_attribute)
with no value or a value of `"true"`, or if the result of
[`checkValidity()`](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5/Constraint_validation)
is `false`.

#### Examples

```html
<input data-testid="no-aria-invalid" />
<input data-testid="aria-invalid" aria-invalid />
<input data-testid="aria-invalid-value" aria-invalid="true" />
<input data-testid="aria-invalid-false" aria-invalid="false" />

<form data-testid="valid-form">
  <input />
</form>

<form data-testid="invalid-form">
  <input required />
</form>
```

```javascript
expect(getByTestId('no-aria-invalid')).not.toBeInvalid()
expect(getByTestId('aria-invalid')).toBeInvalid()
expect(getByTestId('aria-invalid-value')).toBeInvalid()
expect(getByTestId('aria-invalid-false')).not.toBeInvalid()

expect(getByTestId('valid-form')).not.toBeInvalid()
expect(getByTestId('invalid-form')).toBeInvalid()
```

<hr />

### `toBeRequired`

```typescript
toBeRequired()
```

This allows you to check if a form element is currently required.

An element is required if it is having a `required` or `aria-required="true"`
attribute.

#### Examples

```html
<input data-testid="required-input" required />
<input data-testid="aria-required-input" aria-required="true" />
<input data-testid="conflicted-input" required aria-required="false" />
<input data-testid="aria-not-required-input" aria-required="false" />
<input data-testid="optional-input" />
<input data-testid="unsupported-type" type="image" required />
<select data-testid="select" required></select>
<textarea data-testid="textarea" required></textarea>
<div data-testid="supported-role" role="tree" required></div>
<div data-testid="supported-role-aria" role="tree" aria-required="true"></div>
```

```javascript
expect(getByTestId('required-input')).toBeRequired()
expect(getByTestId('aria-required-input')).toBeRequired()
expect(getByTestId('conflicted-input')).toBeRequired()
expect(getByTestId('aria-not-required-input')).not.toBeRequired()
expect(getByTestId('optional-input')).not.toBeRequired()
expect(getByTestId('unsupported-type')).not.toBeRequired()
expect(getByTestId('select')).toBeRequired()
expect(getByTestId('textarea')).toBeRequired()
expect(getByTestId('supported-role')).not.toBeRequired()
expect(getByTestId('supported-role-aria')).toBeRequired()
```

<hr />

### `toBeValid`

```typescript
toBeValid()
```

This allows you to check if the value of a form element, or the entire `form`,
is currently valid.

An `input`, `select`, `textarea`, or `form` element is valid if it has no
[`aria-invalid` attribute](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-invalid_attribute)
or an attribute value of `"false"`. The result of
[`checkValidity()`](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5/Constraint_validation)
must also be `true`.

#### Examples

```html
<input data-testid="no-aria-invalid" />
<input data-testid="aria-invalid" aria-invalid />
<input data-testid="aria-invalid-value" aria-invalid="true" />
<input data-testid="aria-invalid-false" aria-invalid="false" />

<form data-testid="valid-form">
  <input />
</form>

<form data-testid="invalid-form">
  <input required />
</form>
```

```javascript
expect(getByTestId('no-aria-invalid')).toBeValid()
expect(getByTestId('aria-invalid')).not.toBeValid()
expect(getByTestId('aria-invalid-value')).not.toBeValid()
expect(getByTestId('aria-invalid-false')).toBeValid()

expect(getByTestId('valid-form')).toBeValid()
expect(getByTestId('invalid-form')).not.toBeValid()
```

<hr />

### `toBeVisible`

```typescript
toBeVisible()
```

This allows you to check if an element is currently visible to the user.

An element is visible if **all** the following conditions are met:

- it does not have its css property `display` set to `none`
- it does not have its css property `visibility` set to either `hidden` or
  `collapse`
- it does not have its css property `opacity` set to `0`
- its parent element is also visible (and so on up to the top of the DOM tree)
- it does not have the
  [`hidden`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/hidden)
  attribute
- if `<details />` it has the `open` attribute

#### Examples

```html
<div data-testid="zero-opacity" style="opacity: 0">Zero Opacity Example</div>
<div data-testid="visibility-hidden" style="visibility: hidden">
  Visibility Hidden Example
</div>
<div data-testid="display-none" style="display: none">Display None Example</div>
<div style="opacity: 0">
  <span data-testid="hidden-parent">Hidden Parent Example</span>
</div>
<div data-testid="visible">Visible Example</div>
<div data-testid="hidden-attribute" hidden>Hidden Attribute Example</div>
```

```javascript
expect(getByText('Zero Opacity Example')).not.toBeVisible()
expect(getByText('Visibility Hidden Example')).not.toBeVisible()
expect(getByText('Display None Example')).not.toBeVisible()
expect(getByText('Hidden Parent Example')).not.toBeVisible()
expect(getByText('Visible Example')).toBeVisible()
expect(getByText('Hidden Attribute Example')).not.toBeVisible()
```

<hr />

### `toContainElement`

```typescript
toContainElement(element: HTMLElement | SVGElement | null)
```

This allows you to assert whether an element contains another element as a
descendant or not.

#### Examples

```html
<span data-testid="ancestor"><span data-testid="descendant"></span></span>
```

```javascript
const ancestor = getByTestId('ancestor')
const descendant = getByTestId('descendant')
const nonExistantElement = getByTestId('does-not-exist')

expect(ancestor).toContainElement(descendant)
expect(descendant).not.toContainElement(ancestor)
expect(ancestor).not.toContainElement(nonExistantElement)
```

<hr />

### `toContainHTML`

```typescript
toContainHTML(htmlText: string)
```

Assert whether a string representing a HTML element is contained in another
element:

#### Examples

```html
<span data-testid="parent"><span data-testid="child"></span></span>
```

```javascript
expect(getByTestId('parent')).toContainHTML('<span data-testid="child"></span>')
```

> Chances are you probably do not need to use this matcher. We encourage testing
> from the perspective of how the user perceives the app in a browser. That's
> why testing against a specific DOM structure is not advised.
>
> It could be useful in situations where the code being tested renders html that
> was obtained from an external source, and you want to validate that that html
> code was used as intended.
>
> It should not be used to check DOM structure that you control. Please use
> [`toContainElement`](#tocontainelement) instead.

<hr />

### `toHaveAttribute`

```typescript
toHaveAttribute(attr: string, value?: any)
```

This allows you to check whether the given element has an attribute or not. You
can also optionally check that the attribute has a specific expected value or
partial match using
[expect.stringContaining](https://jestjs.io/docs/en/expect.html#expectnotstringcontainingstring)/[expect.stringMatching](https://jestjs.io/docs/en/expect.html#expectstringmatchingstring-regexp)

#### Examples

```html
<button data-testid="ok-button" type="submit" disabled>ok</button>
```

```javascript
const button = getByTestId('ok-button')

expect(button).toHaveAttribute('disabled')
expect(button).toHaveAttribute('type', 'submit')
expect(button).not.toHaveAttribute('type', 'button')

expect(button).toHaveAttribute('type', expect.stringContaining('sub'))
expect(button).toHaveAttribute('type', expect.not.stringContaining('but'))
```

<hr />

### `toHaveClass`

```typescript
toHaveClass(...classNames: string[], options?: {exact: boolean})
```

This allows you to check whether the given element has certain classes within
its `class` attribute.

You must provide at least one class, unless you are asserting that an element
does not have any classes.

#### Examples

```html
<button data-testid="delete-button" class="btn extra btn-danger">
  Delete item
</button>
<button data-testid="no-classes">No Classes</button>
```

```javascript
const deleteButton = getByTestId('delete-button')
const noClasses = getByTestId('no-classes')

expect(deleteButton).toHaveClass('extra')
expect(deleteButton).toHaveClass('btn-danger btn')
expect(deleteButton).toHaveClass('btn-danger', 'btn')
expect(deleteButton).not.toHaveClass('btn-link')

expect(deleteButton).toHaveClass('btn-danger extra btn', {exact: true}) // to check if the element has EXACTLY a set of classes
expect(deleteButton).not.toHaveClass('btn-danger extra', {exact: true}) // if it has more than expected it is going to fail

expect(noClasses).not.toHaveClass()
```

<hr />

### `toHaveFocus`

```typescript
toHaveFocus()
```

This allows you to assert whether an element has focus or not.

#### Examples

```html
<div><input type="text" data-testid="element-to-focus" /></div>
```

```javascript
const input = getByTestId('element-to-focus')

input.focus()
expect(input).toHaveFocus()

input.blur()
expect(input).not.toHaveFocus()
```

<hr />

### `toHaveFormValues`

```typescript
toHaveFormValues(expectedValues: {
  [name: string]: any
})
```

This allows you to check if a form or fieldset contains form controls for each
given name, and having the specified value.

> It is important to stress that this matcher can only be invoked on a [form][]
> or a [fieldset][] element.
>
> This allows it to take advantage of the [.elements][] property in `form` and
> `fieldset` to reliably fetch all form controls within them.
>
> This also avoids the possibility that users provide a container that contains
> more than one `form`, thereby intermixing form controls that are not related,
> and could even conflict with one another.

This matcher abstracts away the particularities with which a form control value
is obtained depending on the type of form control. For instance, `<input>`
elements have a `value` attribute, but `<select>` elements do not. Here's a list
of all cases covered:

- `<input type="number">` elements return the value as a **number**, instead of
  a string.
- `<input type="checkbox">` elements:
  - if there's a single one with the given `name` attribute, it is treated as a
    **boolean**, returning `true` if the checkbox is checked, `false` if
    unchecked.
  - if there's more than one checkbox with the same `name` attribute, they are
    all treated collectively as a single form control, which returns the value
    as an **array** containing all the values of the selected checkboxes in the
    collection.
- `<input type="radio">` elements are all grouped by the `name` attribute, and
  such a group treated as a single form control. This form control returns the
  value as a **string** corresponding to the `value` attribute of the selected
  radio button within the group.
- `<input type="text">` elements return the value as a **string**. This also
  applies to `<input>` elements having any other possible `type` attribute
  that's not explicitly covered in different rules above (e.g. `search`,
  `email`, `date`, `password`, `hidden`, etc.)
- `<select>` elements without the `multiple` attribute return the value as a
  **string** corresponding to the `value` attribute of the selected `option`, or
  `undefined` if there's no selected option.
- `<select multiple>` elements return the value as an **array** containing all
  the values of the [selected options][].
- `<textarea>` elements return their value as a **string**. The value
  corresponds to their node content.

The above rules make it easy, for instance, to switch from using a single select
control to using a group of radio buttons. Or to switch from a multi select
control, to using a group of checkboxes. The resulting set of form values used
by this matcher to compare against would be the same.

[selected options]:
  https://developer.mozilla.org/en-US/docs/Web/API/HTMLSelectElement/selectedOptions
[form]: https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement
[fieldset]: https://developer.mozilla.org/en-US/docs/Web/API/HTMLFieldSetElement
[.elements]:
  https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/elements

#### Examples

```html
<form data-testid="login-form">
  <input type="text" name="username" value="jane.doe" />
  <input type="password" name="password" value="12345678" />
  <input type="checkbox" name="rememberMe" checked />
  <button type="submit">Sign in</button>
</form>
```

```javascript
expect(getByTestId('login-form')).toHaveFormValues({
  username: 'jane.doe',
  rememberMe: true,
})
```

### `toHaveStyle`

```typescript
toHaveStyle(css: string | object)
```

This allows you to check if a certain element has some specific css properties
with specific values applied. It matches only if the element has _all_ the
expected properties applied, not just some of them.

#### Examples

```html
<button
  data-testid="delete-button"
  style="display: none; background-color: red"
>
  Delete item
</button>
```

```javascript
const button = getByTestId('delete-button')

expect(button).toHaveStyle('display: none')
expect(button).toHaveStyle({display: 'none'})
expect(button).toHaveStyle(`
  background-color: red;
  display: none;
`)
expect(button).toHaveStyle({
  backgroundColor: 'red',
  display: 'none',
})
expect(button).not.toHaveStyle(`
  background-color: blue;
  display: none;
`)
expect(button).not.toHaveStyle({
  backgroundColor: 'blue',
  display: 'none',
})
```

This also works with rules that are applied to the element via a class name for
which some rules are defined in a stylesheet currently active in the document.
The usual rules of css precedence apply.

<hr />

### `toHaveTextContent`

```typescript
toHaveTextContent(text: string | RegExp, options?: {normalizeWhitespace: boolean})
```

This allows you to check whether the given element has a text content or not.

When a `string` argument is passed through, it will perform a partial
case-sensitive match to the element content.

To perform a case-insensitive match, you can use a `RegExp` with the `/i`
modifier.

If you want to match the whole content, you can use a `RegExp` to do it.

#### Examples

```html
<span data-testid="text-content">Text Content</span>
```

```javascript
const element = getByTestId('text-content')

expect(element).toHaveTextContent('Content')
expect(element).toHaveTextContent(/^Text Content$/) // to match the whole content
expect(element).toHaveTextContent(/content$/i) // to use case-insensitive match
expect(element).not.toHaveTextContent('content')
```

<hr />

### `toHaveValue`

```typescript
toHaveValue(value: string | string[] | number)
```

This allows you to check whether the given form element has the specified value.
It accepts `<input>`, `<select>` and `<textarea>` elements with the exception of
`<input type="checkbox">` and `<input type="radio">`, which can be meaningfully
matched only using [`toBeChecked`](#tobechecked) or
[`toHaveFormValues`](#tohaveformvalues).

For all other form elements, the value is matched using the same algorithm as in
[`toHaveFormValues`](#tohaveformvalues) does.

#### Examples

```html
<input type="text" value="text" data-testid="input-text" />
<input type="number" value="5" data-testid="input-number" />
<input type="text" data-testid="input-empty" />
<select data-testid="multiple" multiple data-testid="select-number">
  <option value="first">First Value</option>
  <option value="second" selected>Second Value</option>
  <option value="third" selected>Third Value</option>
</select>
```

##### Using DOM Testing Library

```javascript
const textInput = getByTestId('input-text')
const numberInput = getByTestId('input-number')
const emptyInput = getByTestId('input-empty')
const selectInput = getByTestId('select-number')

expect(textInput).toHaveValue('text')
expect(numberInput).toHaveValue(5)
expect(emptyInput).not.toHaveValue()
expect(selectInput).not.toHaveValue(['second', 'third'])
```

<hr />

### `toHaveDisplayValue`

```typescript
toHaveDisplayValue(value: string | RegExp | (string|RegExp)[])
```

This allows you to check whether the given form element has the specified
displayed value (the one the end user will see). It accepts `<input>`,
`<select>` and `<textarea>` elements with the exception of
`<input type="checkbox">` and `<input type="radio">`, which can be meaningfully
matched only using [`toBeChecked`](#tobechecked) or
[`toHaveFormValues`](#tohaveformvalues).

#### Examples

```html
<label for="input-example">First name</label>
<input type="text" id="input-example" value="Luca" />

<label for="textarea-example">Description</label>
<textarea id="textarea-example">An example description here.</textarea>

<label for="single-select-example">Fruit</label>
<select id="single-select-example">
  <option value="">Select a fruit...</option>
  <option value="banana">Banana</option>
  <option value="ananas">Ananas</option>
  <option value="avocado">Avocado</option>
</select>

<label for="mutiple-select-example">Fruits</label>
<select id="multiple-select-example" multiple>
  <option value="">Select a fruit...</option>
  <option value="banana" selected>Banana</option>
  <option value="ananas">Ananas</option>
  <option value="avocado" selected>Avocado</option>
</select>
```

##### Using DOM Testing Library

```javascript
const input = screen.getByLabelText('First name')
const textarea = screen.getByLabelText('Description')
const selectSingle = screen.getByLabelText('Fruit')
const selectMultiple = screen.getByLabelText('Fruits')

expect(input).toHaveDisplayValue('Luca')
expect(input).toHaveDisplayValue(/Luc/)
expect(textarea).toHaveDisplayValue('An example description here.')
expect(textarea).toHaveDisplayValue(/example/)
expect(selectSingle).toHaveDisplayValue('Select a fruit...')
expect(selectSingle).toHaveDisplayValue(/Select/)
expect(selectMultiple).toHaveDisplayValue([/Avocado/, 'Banana'])
```

<hr />

### `toBeChecked`

```typescript
toBeChecked()
```

This allows you to check whether the given element is checked. It accepts an
`input` of type `checkbox` or `radio` and elements with a `role` of `checkbox`,
`radio` or `switch` with a valid `aria-checked` attribute of `"true"` or
`"false"`.

#### Examples

```html
<input type="checkbox" checked data-testid="input-checkbox-checked" />
<input type="checkbox" data-testid="input-checkbox-unchecked" />
<div role="checkbox" aria-checked="true" data-testid="aria-checkbox-checked" />
<div
  role="checkbox"
  aria-checked="false"
  data-testid="aria-checkbox-unchecked"
/>

<input type="radio" checked value="foo" data-testid="input-radio-checked" />
<input type="radio" value="foo" data-testid="input-radio-unchecked" />
<div role="radio" aria-checked="true" data-testid="aria-radio-checked" />
<div role="radio" aria-checked="false" data-testid="aria-radio-unchecked" />
<div role="switch" aria-checked="true" data-testid="aria-switch-checked" />
<div role="switch" aria-checked="false" data-testid="aria-switch-unchecked" />
```

```javascript
const inputCheckboxChecked = getByTestId('input-checkbox-checked')
const inputCheckboxUnchecked = getByTestId('input-checkbox-unchecked')
const ariaCheckboxChecked = getByTestId('aria-checkbox-checked')
const ariaCheckboxUnchecked = getByTestId('aria-checkbox-unchecked')
expect(inputCheckboxChecked).toBeChecked()
expect(inputCheckboxUnchecked).not.toBeChecked()
expect(ariaCheckboxChecked).toBeChecked()
expect(ariaCheckboxUnchecked).not.toBeChecked()

const inputRadioChecked = getByTestId('input-radio-checked')
const inputRadioUnchecked = getByTestId('input-radio-unchecked')
const ariaRadioChecked = getByTestId('aria-radio-checked')
const ariaRadioUnchecked = getByTestId('aria-radio-unchecked')
expect(inputRadioChecked).toBeChecked()
expect(inputRadioUnchecked).not.toBeChecked()
expect(ariaRadioChecked).toBeChecked()
expect(ariaRadioUnchecked).not.toBeChecked()

const ariaSwitchChecked = getByTestId('aria-switch-checked')
const ariaSwitchUnchecked = getByTestId('aria-switch-unchecked')
expect(ariaSwitchChecked).toBeChecked()
expect(ariaSwitchUnchecked).not.toBeChecked()
```

<hr />

### `toHaveDescription`

```typescript
toHaveDescription(text: string | RegExp)
```

This allows you to check whether the given element has a description or not.

An element gets its description via the
[`aria-describedby` attribute](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-describedby_attribute).
Set this to the `id` of one or more other elements. These elements may be nested
inside, be outside, or a sibling of the passed in element.

Whitespace is normalized. Using multiple ids will
[join the referenced elements’ text content separated by a space](https://www.w3.org/TR/accname-1.1/#mapping_additional_nd_description).

When a `string` argument is passed through, it will perform a whole
case-sensitive match to the description text.

To perform a case-insensitive match, you can use a `RegExp` with the `/i`
modifier.

To perform a partial match, you can pass a `RegExp` or use
`expect.stringContaining("partial string")`.

#### Examples

```html
<button aria-label="Close" aria-describedby="description-close">
  X
</button>
<div id="description-close">
  Closing will discard any changes
</div>

<button>Delete</button>
```

```javascript
const closeButton = getByRole('button', {name: 'Close'})

expect(closeButton).toHaveDescription('Closing will discard any changes')
expect(closeButton).toHaveDescription(/will discard/) // to partially match
expect(closeButton).toHaveDescription(expect.stringContaining('will discard')) // to partially match
expect(closeButton).toHaveDescription(/^closing/i) // to use case-insensitive match
expect(closeButton).not.toHaveDescription('Other description')

const deleteButton = getByRole('button', {name: 'Delete'})
expect(deleteButton).not.toHaveDescription()
expect(deleteButton).toHaveDescription('') // Missing or empty description always becomes a blank string
```

## Deprecated matchers

### `toBeInTheDOM`

```typescript
toBeInTheDOM()
```

This allows you to check whether a value is a DOM element, or not.

Contrary to what its name implies, this matcher only checks that you passed to
it a valid DOM element. It does not have a clear definition of what "the DOM"
is. Therefore, it does not check whether that element is contained anywhere.

This is the main reason why this matcher is deprecated, and will be removed in
the next major release. You can follow the discussion around this decision in
more detail [here](https://github.com/testing-library/jest-dom/issues/34).

As an alternative, you can use [`toBeInTheDocument`](#tobeinthedocument) or
[`toContainElement`](#tocontainelement). Or if you just want to check if a value
is indeed an `HTMLElement` you can always use some of
[jest's built-in matchers](https://jestjs.io/docs/en/expect#tobeinstanceofclass):

```js
expect(document.querySelector('.ok-button')).toBeInstanceOf(HTMLElement)
expect(document.querySelector('.cancel-button')).toBeTruthy()
```

> Note: The differences between `toBeInTheDOM` and `toBeInTheDocument` are
> significant. Replacing all uses of `toBeInTheDOM` with `toBeInTheDocument`
> will likely cause unintended consequences in your tests. Please make sure when
> replacing `toBeInTheDOM` to read through the documentation of the proposed
> alternatives to see which use case works better for your needs.

## Inspiration

This whole library was extracted out of Kent C. Dodds' [DOM Testing
Library][dom-testing-library], which was in turn extracted out of [React Testing
Library][react-testing-library].

The intention is to make this available to be used independently of these other
libraries, and also to make it more clear that these other libraries are
independent from jest, and can be used with other tests runners as well.

## Other Solutions

I'm not aware of any, if you are please [make a pull request][prs] and add it
here!

If you would like to further test the accessibility and validity of the DOM
consider [`jest-axe`](https://github.com/nickcolley/jest-axe). It doesn't
overlap with `jest-dom` but can complement it for more in-depth accessibility
checking (eg: validating `aria` attributes or ensuring unique id attributes).

## Guiding Principles

> [The more your tests resemble the way your software is used, the more
> confidence they can give you.][guiding-principle]

This library follows the same guiding principles as its mother library [DOM
Testing Library][dom-testing-library]. Go [check them out][guiding-principle]
for more details.

Additionally, with respect to custom DOM matchers, this library aims to maintain
a minimal but useful set of them, while avoiding bloating itself with merely
convenient ones that can be easily achieved with other APIs. In general, the
overall criteria for what is considered a useful custom matcher to add to this
library, is that doing the equivalent assertion on our own makes the test code
more verbose, less clear in its intent, and/or harder to read.

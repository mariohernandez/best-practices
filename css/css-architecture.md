---
title: CSS architecture
description: Keep your style files in order so others know what is going on.
baseurl: ../../../
nav_groups:
- primary
- secondary
- tertiary
---

## Contents
* Goals
* [Component Practices](#component-practices)
  * [Defining components](#defining-components)
  * [Name Components using design semantics](#name-components-using-design-semantics)
  * [Extending Components using modifier practices](#extending-components-using-modifier-classes)
  * [Modifying components based on context](#modifying-components-based-on-context)
  * [Relying on HTML structure](#relying-on-html-structure)
  * [Generic Class Names](#generic-class-names)
* [General Best Practices](#general-best-practices)
  * [Iterative Enhancement](#iterative-enhancement)
  * [Ruleset Size](#ruleset-size)
  * [Combinators (multi-part selectors)](#combinators-multi-part-selectors)
  * [Separate Concerns](#separate-concerns)
  * [Specificity Scope and Range](#specificity-scope-and-range)
  * [Declaration Order](#declaration-order)
  * [Nesting](#nesting)

## Goals

The goals of good CSS should not be different from those of good software engineering. Well-architected CSS, like PHP or JavaScript, should be:

<dl>
  <dt>Predictable</dt>
  <dd>CSS and contributed components should be consistent and understandable. Changes should do what you would expect without side-effects.</dd>

  <dt>Reusable</dt>
  <dd>CSS rules should be abstract and decoupled enough that you can build new components quickly from existing parts without having to recode patterns and problems you’ve already solved.</dd>

  <dt>Maintainable</dt>
  <dd>As new components and features are needed, it should be easy to add, modify and extend CSS without breaking (or refactoring) existing styles.</dd>

  <dt>Scalable</dt>
  <dd>CSS should be easy to manage for a single developer or for large, distributed teams (like Drupal’s).</dd>
</dl>

## The Component

Components are the discrete, purpose-built visual elements that make up the UI of a site or app. Components consist of HTML, CSS, and often – but not always – JavaScript. They are our navbars, dialogs, buttons and carousels. Components can be simple (such as icon containers and buttons) or complex enough to be themselves composed of other components.

## Component Practices

To better understand the best practices provided below, it can be helpful to review some common approaches that impede our goals of predictability, maintainability, reusability and scalability.

### Defining components

_avoid_:
* Relying on markup structure and overly-generic class names
* Reflecting the DOM structure in the class name

```
.menu li a {}
.menu__item__link {}
```

_Prefer_:
* Defining a component’s elements explicitly; prefixing them with the component’s name followed by two underscores (if following BEM):
* Defining complex relationships _as needed_.

```css
.component {}

/* Component elements */
.component__header {}
.component__body {}

.menu__link {}
```

**General Principle**
Establish the necessary relationships between elements.

#### Consider using BEM to reflect content model relationships as well as design relationships

Assuming this data from a CMS:
```json
{
  "BlogPost": {
    "Title": "Introducing HTML",
    "Author": "Eric Meyer",
    "PublishedTime": "2024-07-01T05:00:00.000Z",
    "isPinned": true
  }
}
```

One might produce this markup
```html
<article class="blogPost blogPost--isPinned">
  <header class="blogPost__header">
    <h1 class="blogPost__title">Introducing HTML</h1>
    <p class="blogPost__author">Eric Meyer</p>
    <time class="blogPost__publishedTime">June 31st, 2024</time>
  </header>
</article>
```

And therefore one may want to describe it this way in the CSS:

```css

  block: blogPost
  elements:
    __header !!
      __title
      __author
      __publishedTime
  modifiers:
    --isPinned
```

### Extending components using modifier classes

*Avoid*
* Modifying modifiers
* Modifier names that describe the visual state and not purpose

```html
<!-- button with variants that have overlaping concerns and also extend modifiers -->
<button class="button button--bigger button--primary--green">Save</button>
```

```css
.button--big {
  font-size: 1.1em;
}

.button--bigger {
  font-size: 1.5em;
}

.button--primary--green {
  font-size: 1.2em;
  background-color: #00ff00;
}
```

*Prefer*
* Creating component variants explicitly, adding a suffix with the variant name preceded by two dashes (if using BEM).
**  this modifier class should only contain the styles needed to extend the original.
* Modifier names that describe purpose


```html
<!-- Button variant is created by applying both component and modifier classes -->
<button class="button button--primary">Save</button>
```


```css
/* Button component */
.button {
  font-size: 1em;
  background-color: #0000ff;
}

/* Button modifier class */
.button--primary {
  font-size: 1.2em;
}

.button--green {
  background-color: #00ff00;
}

.button--success {
  background-color: #00ff00;
  font-size: 1.2em;
}
```

**General Principle**
Be explicit, reusable, and clear about intent.

### Name Components Using Design Semantics
**Avoid**
* Class names that mirror the HTML element they are used on
* Class names that exist solely for providing content semantics

```css
.article { // an element called <article exists>
  display: block;
}
.paragraph { // generic content semantics
  font-family: Helvetica;
}
```

**Prefer**
* Class names that communicate useful information to developers
* Class names that reflect design semantics
*  [Microformats](http://microformats.org/) and/or microdata for reflecting detailed content semantics

Class names that communicate useful information to developers might use BEM and be based on a content model. This HTML sample would tell a developer what fields from the CMS provide the content.


Assuming this data from a CMS:
```json
{
  "BlogPost": {
    "Title": "Introducing HTML",
    "Author": "Eric Meyer",
    "PublishedTime": "2024-07-01T05:00:00.000Z"
  }
}
```

One might produce these classes:
```html
<article class="blogPost">
  <h1 class="blogPost__title">Introducing HTML</h1>
  <p class="blogPost__author">Eric Meyer</p>
  <time class="blogPost__publishedTime">June 31st, 2024</time>
</article>
```

Class names that communicate design semantics could use SMACSS or OOCSS to reflect design. This sample HTML communicates fields from the CMS, and informs the developer what the presentation is:

```html
<article class="blogPost card">
  <h1 class="blogPost__title card__title">Introducing HTML</h1>
  <p class="blogPost__author card__meta">Eric Meyer</p>
  <time class="blogPost__publishedTime card__meta">June 31st, 2024</time>
</article>
```

By reserving class names for developer communication and design information, the author is now able to use appropriate microdata attributes to  augment semantics:
```html
<article class="blogPost card" itemscope itemtype="http://schema.org/blogPost">
  <h1 class="blogPost__title card__title" itemprop="title">Introducing HTML</h1>
  <p class="blogPost__author card__meta" itemprop="author">Eric Meyer</p>
  <time class="blogPost__publishedTime card__meta" itemprop="datePublished">June 31st, 2024</time>
</article>
```

**General Principle**
Use the appropriate domain and feature to communicate the appropriate information.


### Modifying components based on context
* Avoid modifying based on a rigid assumption of the markup
* Avoid modifying based on a rigid assumption of how classes will be aplied
* Prefer creating a clearly defined context

_Avoid_:
```css
/* Modifying a component when it’s in a sidebar. */
.sidebar .component {}
```

_Prefer_:
```css
.sidebar {}
.component {}
/*A firm and predictable context is established that doesn't make assumptions*/
.sidebar__component {}
```

**Principle**
Shorter (less complex) selectors are more efficient for reading, expressing intent, and avoiding unintended side-effects.

### Relying on HTML structure
Mirroring a markup structure in our CSS selectors makes the resulting styles easy to break (with markup changes) and hard to reuse (because it’s tied to very specific HTML).

_Avoid_:

*   Overly complex selectors: `nav > ul > li > a`, `article p:first-child`
*   Qualified (chained) selectors: `a.button`, `ul.nav`

_Prefer_:

* Fully qualified selectors: `.nav__listItemLink`, `.article__paragraph--first`
* Selectors that communicate intent: `.article__paragraph--dropCap`

**Principle**
Write CSS that works when the structure of HTML changes or the HTML elements themselves change. Write CSS that tells a developer, "this is safe to use on many arrangements of markup".

### Generic class names

_Avoid_:
* Words that prescribe the content
* Words that prescribe the markup
* Ambiguously describing scope or presentation
* Using words to describe variation or relationships

```css
.widget {}
.widget .title {}
.widget .content {}
```

This CSS might seem economical, but tends to be counterproductive: `.title` and `.content` are too generic. A stand-alone `.title` component created later will affect widget titles, likely without intending to.

_Prefer_:
* Words that describe the purpose
* Describing scope
* Consider abbreviating scope if meaning can be obvious
* Convention to prescribe variations
* Convention to describe relationships

```css
.callToAction {}
.callToAction__title {}
.callToAction__richText {}
```

This CSS describes the content it's being used. A developer will understand the intent and the context in which these are used.

**Principle**
Write class names that answer the question, “What content could I use this on and how will it present that content?”


## General Best Practices

* Use CSS to define the appearance of an element anywhere and everywhere it can be consumed
  * screen renderer
  * screen reader
  * print
* Use classes to assign appearance to markup
* Write selectors for the context in which they are being parsed
  * CSS parses right-to-left; the rightmost selector is targeted therefore shorter selectors are better
  * JS parses left-to right where longer selectors will be more performant


**Be Pragmatic, not dogmatic**
* Use the cascade the way it was designed with incrementally more specific selectors
* Use selectors and their operators as solutions to markup problems

### Iterative Enhancement

_Avoid_:
* Creating style rules that undo other rules,

```
.component-no-padding {
  padding: 0 !important;
}

```

_Prefer_:
* More specific class names based on context
```
.component__collapsed {
  padding: 0;
}
```

**General Principles**
Write CSS that is less-complex, easier to understand, and has less risk of bloating the stylesheet or specificity.

### Ruleset Size

_Avoid_:
* Mixing intentions of the presentation
* Combining how a user consumes content with how they interact with it
* Having to raise specificity to opt-out of parts of the presentation



```css
.callToAction {
  display: flex;
  justify-content: center;
  font-size: var(--fontSizeBigger);
  padding: 0.618em 0;
  margin: 0.618em 0;
  background: rgba(120, 120, 120, .3);
  border: 3px solid rgb(120, 120, 120);
  transform: 0.3s ease-in-out;
}
```

_Prefer_:
* Small units of presentation that target a particular intent
* Separating consumption of content from interaction with content
* Being able to opt-in to presentation features

```css
/* Layout */
.callToAction {
  display: flex;
  justify-content: center;
  padding: 0.618em 0;
  margin: 0.618em 0;
}

/* Size variation */
.callToAction--bigger{
  font-size: var(--fontSizeBigger);
}

/* Neutral theme variation */
.callToAction--neutral {
  background: rgba(120, 120, 120, .3);
  border: 3px solid rgb(120, 120, 120);
}

/* Interactive variation */
.callToAction--isInteractive {
  transform: 0.3s ease-in-out;
}
```

**General Principle**
Structure your CSS by the priorities of the renderer favoring layout before theme or skin. Separate properties by whether they address how the user consumes content and how they interact with it, because interactions change with devices.

Applying positioning, margins, padding, colors, borders and text styles all in a single rule overloads the rule, making it difficult or impossible to reuse if some parts (say, background, borders and padding) need to be applied to a similar component later on.


### Combinators (multi-part selectors)
Combinators are selector operators that allow the developer to target elements based on some construction of the markup. While a general practice may be to avoid these, there are times when one of these combinators is the most pragmatic choice.

```
.foo .bar {} /* Descendant combinator */
.foo > .bar {} /* child combinator */
.foo ~ .bar {} /* general sibling selector */
.foo + .bar {} /* adjacent sibling selector */
col || td {} /* Column combinator */
```

_Avoid_:
*  elements with no native semantics (div, span) in multi-part selectors.
* The descendent selector, especially for components that may wrap other components.
* More than 2 [combinators](http://reference.sitepoint.com/css/combinators) in a selector.
* Using combinators when you have the option to modify the markup


```
.my-list li {
  display: block;
}

.my-list li a {
  text-decoration: none;
}

.slat ~ .slat {
  border-top: 1px solid #cccccc;
}
```

_Prefer_:
* Adding classes to markup
* Styling the element directly
* The combinator that _most specifically_ targets the selector


```css
.my-list > li {
 display: block;
}

.my-list a {
  text-decoration: none;
}

.slat + .slat {
  border-top: 1px solid #cccccc;
}
```


**General Principle**

Be explicit.

### Pseudo-class selectors
Pseudo-class selectors are keywords that allow a developer to target elements based on state, attribute, interaction, or some other aspect.

Some of the most common selectors are the user action classes (`:hover`, `:focus`, and `:active`).

Pseudo-class selectors are also usually _chained_ to an element (e.g.: `a:hover` as opposed to `:hover`).

Pseudo-class selectors fall into nine categories:

* Element state
  * `:fullscreen`, `:modal`
* Input
  * `:enabled`, `:checked`
* Linguistic
  * `:dir()`, `:lang()`
* Location
  * `:link`, `:target`,
* Resource state
  * `:playing`, `:paused`
* Time-dimensional
  * `:current`, `:future`
* Tree structural
  * `:root`, `:nth-of-child`
* User action
  * `:hover`, `:active`
* Functional
  * `:is()`, `:not()`

In general, a developer should favor a pseudo-class selector over _creating_ a class with JavaScript that targets the same state (and named the same way!). A developer is encouraged to consult the[ complete list of pseudo-classes](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes#alphabetical_index) to see if an appropriate pseudo-class exists, first.

_Avoid_:

```HTML
<input  onfocus= "(evt) => {evt.target.classList.add('focus')"} />
```
```CSS
input.focus {
  outline: 1px solid blue;
}
```

_Prefer_:
```
.input:focus {
  outline: 1px solid blue;
}
```

**General Principle**
Use the browser's native capabilities in their _simplest available form_ &mdash; which is the one with the least amount of parts.

#### Functional pseudo-class selectors
The functional pseudo-class selectors include
* `:is()`
* `:not()`
* `:where()`
* `:has()`

These are so called because they are selectors that are _functions_; they accept fully qualified selectors as arguments.

Functional pseudo-classes have additional guidance _because_  their impact on specificity is inconsistent with the other categories of pseudo classes.

Developers should be more cautious with using functional pseudo-classes.

_Avoid_:

Unchained pseudo-classes: `:not(.article), :where(address)`

_Because_:

Unchained pseudo-classes broaden the scope of affected elements, which likely to cause unintended consequences (i.e. side-effects).

_Prefer_:

A small and tight scope with clear selector intent:
```css
.articleContainer .header,
.articleContainer .footer {}
```

_Avoid :is()_:

 `.article:is(#art-123, .bar, article)`

_Because_:

`:is()` uses the most-specific argument in the list to determine specificity, which means that `.article:is(#art-123, section)` will give `section.article` the specificity 1,1,0.

_Prefer_:

Combining selectors in a list so that each element receives its own specificity.
```css
#art-123,
section.article {}
```

_Avoid `:not()`_:

 ```css
article:not(#art-123){}
nav li:not(:last-child){}
```

_Because_:

`:not()` will take the specificity of its argument and then apply that to _everything that does not match the argument_.

For example:
```css
article:not(#art-123)
```

will apply its style to _every_ `article` except one with the specificity of an id (`1,0,1`).

_Prefer_:

Clear selector intent:

```css
article {}
#art-123 {}
nav li {}
nav li:last-child {}
```

_Avoid_ `:where()` for overrides :

```css
a {
  text-decoration: 1px solid blue;
}
a:where(:hover, :focus) {
  text-decoration: none
}
```

_Because_:

`:where()` does not contribute specificity _at all_.  The specificity of `a:where(:hover)` will be (0,0,1).

When this selector is used to overwrite a previous rule, it will only work so long as it comes later in the cascade, which makes it fragile.

_Prefer_:

Introducing new styles at lowest-sensible specificity, and comma separated selectors for overrides.

```css
a:where(:hover, :focus, :active) {
  transition: 0.2s ease-in-out
}

a:hover, a:focus {
  text-decoration: underline;
}
```

_Avoid `:has()` when you have control of markup_:

```html
<section class="container">
  <h2 class="container__title"></h2>
</section>
```
```css
.container:has(h2:empty) {
  padding-top: 1em;
}
```

_Because_:
`:has()` behaves like `:is()` and `:not()` where it takes on the specificity of the most specific selector in the argument.

_Prefer_:
Using `:has()` in areas where you don't have control of the markup, such as rich text fields or in cases where a content author has control of layout.

```html
<div class="rich-text">

</div>
```
```css
.rich-text:has(h2) {
  padding-top: 1em;
}
```

**General Principles**
* Use good selector intent to target only what you need to.
* Be aware of the side-effects of the functional pseudo-class selectors and aim to have as few side-effects as possible


### Separate Concerns

Components should not be responsible for their positioning or layout within the site. Never apply widths or heights except to elements that natively have these properties (e.g. images). Within components, separate structural rules from stylistic rules.

Separate style from behavior by using dedicated classes for JavaScript manipulation rather than relying on classes already in use for CSS. This way, we can modify classes for style purposes without fear of breaking JS, and vice versa. To make the distinction clear, classes used for JavaScript manipulation should be prefixed with 'js-'. These JavaScript hooks must never be used for styling purposes. See the section ‘Formatting Class Names’ for more information on naming conventions.

Avoid applying inline styles using JavaScript. If the behaviour is describing a state change, apply a class name describing the state (e.g. 'is-active'), and allow CSS to provide the appearance. Only use inline styles applied via JavaScript when the value of the style attributes must be computed at runtime.

Elemental uses the [SMACSS](http://smacss.com/book/) system to conceptually categorize CSS rules.

1. Base
Base rules consist of styling for HTML elements only, such as used in a CSS reset or [Normalize.css](http://necolas.github.com/normalize.css/). Base rules should never include class selectors.
To avoid ‘undoing’ styles in components, base styles should reflect the simplest possible appearance of each element. For example, the simplest usage of the `ul` element may be completely unstyled, removing list markers and indents and relying on a component class for other applications.
2. Layout
Arrangement of elements on the page, including grid systems.

> Grid systems should be thought of as shelves. They contain content but are not content in themselves. You put up your shelves then fill them with your stuff [i.e. components]. – _Harry Roberts, [CSS Guidelines](https://github.com/csswizardry/CSS-Guidelines#layout)_</p>

3. Module
Reusable, discrete UI elements; components should form the bulk of Elemental's CSS.
4. State
Styles that deal with transient changes to a component’s appearance. Often, these are client-side changes that occur as the user interacts with the page, such as hovering links or opening a modal dialog. In some cases, states are static for the life of the page and are set from the server, such as the active element in main navigation. The main ways to style state are:

* Custom classes, often but not always applied via JavaScript. These should be prefixed with `.is-`, e.g. `.is-transitioning`, `.is-open`;
* pseudo-classes, such as `:hover` and `:checked`;
* HTML attributes with state semantics, such as `details[open]`;
* media queries: styles that alter appearance based on the immediate browser environment.
  * Theme: Purely visual styling, such as border, box-shadow, colors and backgrounds, font properties, etc. Ideally, these should be separated enough from a component’s structure to be “swappable”, and omitting these entirely should not break the component’s functionality or basic usability.


### Parts of a class name
**Avoid**
* Abbreviations
* Ambiguity in word boundaries

```css

.btn {
 border: 2px solid blue;
 border-radius: 3px;
}

.smallillustration {
  width: 100px;
}

.jsctrlgroup {
  display: flex;
}
```

**Prefer**
* Full words
* A visual separator between words

```css
.button {
  border: 2px solid blue;
  border-radius: 3px;
}

.smallIllustration {
  width: 100px;
}

.js-control-group {
  display: flex;
}
```

** General Principle**
Make class names easy to read — particularly for non-native English speakers.

### Specificity Scope and Range
**Avoid**
* The `id` selector for setting CSS styles
* Using `!important` to solve a specificity problem


```css
#article-container-235 {
  color: blue;
}

.article.card {
  color: blue !important;
}
```


**Prefer**
* Using an `id` outside of the domain of CSS
  * As a performant JavaScript hook (`getElementById()` is faster than `querySelector()`)
  * As an anchor within a document (`<a href="#section-1"></a>`)
  * For adding accessible headers in tables and labels in forms
* Using `!important` proactively for states that must supersede all others
  * Error or disabled states
  * Use-cases within themes
* Selecting an `id` with the specificity of a class if you cannot add a class to the targetted element (`[id="section-1"] {}`)
* Leaving a comment explaining the reason for raising specificity or writing an unusual selector

```css
.notification--error {
  color: red !important; /*error notifications must always stand out from other text */
}

.a11y--dyslexia {
  font-family: "Dyslexie" !important; /* Users with dyslexia need to have a different font */
}

/* markup auto-generated by TerribleLibrary.js */
[id="section-1"] {
  font-weight: normal;
}

```
### Declaration Order
Developers are expected to write code in a consistent and predictable way. This includes the order of CSS properties.

_Avoid_:

* Alphabetical order
* Always adding to the top or bottom of a ruleset

_Prefer_:

* A consistent and predictable order
* Content-first
* Going clockwise around the container (top, right, bottom, left)
* Layout that affects parent & siblings before layout that affects children
* Sizing from the inside out
* Foreground before background
* Interactions and animations at the end

More specifically, the authors prefer this order:

1. Content & Generated Content (list style, quotes, counters, resize, nav-index)
2. Self-alignment, Order
3. Positioning
4. Display, Overflow
5. Child Layout
    1. Flex & Grid
    2. Gap
    3. Columns
6. Box-sizing
    1. Padding
    2. Width & Height
    3. Margin
7. Border & Outline
8. Text
    1. font size, line-height
    2. font family & features
    3. font weight & variations
    4. Alignment, spacing, breaks
    5. Direction & Orphans
9. Background, Shadows
10. Opacity
11. Transitions, Animations
12. Cursors & Pointers

While this is the preferred order for the authors, of greater importance is that the developer maintains consistency within the project.

**General Principles**

* Be consistent.

* Help a developer understand how the element is being laid out before it's seen in the browser.

* Try to minimize FOUC (flash of unstyled content) by giving the browser what's most important for rendering content first.

* Keep properties that would often be changed together (like padding and margin) close so that you reduce the likelihood of duplication.

### Nesting

Nesting is possible in CSS preprocessors and, more recently, CSS. This is a powerful feature that can help write more concise _and_ confusing code. It is important to use nesting in a way that maximizes benefits while minimizing confusion and side-effects.

_Avoid_:

* Nesting more than 3 levels deep
* Nesting for brevity (DRY)

```css
.card {
  .card__header {
    .card__header-title {
      .card__header-title-link {
        color: blue;
      }
    }
  }
}

.link, .field, .button {
  &:hover, &:focus {
    outline: 1px solid blue;
  }
}
```

_Prefer_:

* writing out longer selectors
* Relying on CSS Variables help with DRY / brevity
* Relying on placeholder selectors to help with DRY / brevity
* Nesting for scope

```css
.card__header .card__header-title .card__header-title-link {
  color: blue;
}

:root {
  --userInterestStyle: 1px solid blue;
  --userActiveStyle: 1px solid blue;
}

.link:hover,
.field:hover,
.button:hover {
  outline: var(--userInterestStyle);
}

.link:focus,
.field:focus,
.button:focus {
  outline: var(--userActiveStyle);
}

%interactive:hover {
  outline: var(--userInterestStyle);
}

%interactive:focus {
  outline: var(--userActiveStyle);
}

input, textarea {
  @extend %interactive;
}

.ebookCollection {
  .card__header-title-link {
    color: red;
  }
  .card__footer-button {
    color: orange;
  }
}
```

**General Principle**

Solve problems where they occur. While scope is an end-user concern where it may be valid to increase specificity, DRY & brevity are developer concerns that should be solved in ways that don't impact the complexity of the final selector.

### Nesting with the Parent Selector (`&`)

CSS Preprocessors and the browser have a reserved symbol, `&`, which offers two distinct behaviors:

* In CSS preprocessors it is used for interpolating the parent selector into the child selector
* In the browser, it is a function call to `:is()`

_Avoid_:

* Using the `&` to concatenate selectors
* Placing the `&` anywhere but the beginning of a selector
* Chaining with `&` in any circumstance other than pseudo-classes

```css
.card {
  &__header {
    &__title {
      &__link {
        color: blue;
      }
    }
  }
}

.card__header-title-link {
  .ebookCollection & {
    color: red;
  }
}

[layout*="flex"] {
  &[layout*="top"] {
    align-items: flex-start;
  }
}

```

_Prefer_:

* Complete selectors
* Flatter CSS
* Using `&` for pseudo-classes
* Using `&` with placeholder selectors
* Forward-thinking code that accounts for the [fact that Sass will eventually compile](https://sass-lang.com/blog/sass-and-native-nesting) `&` into `:is()`

```css
.card__header__title__link {
  color: blue;
}

.ebookCollection .card__header-title-link {
  color: red;
}

[layout*="flex"][layout*="top"] {
  align-items: flex-start;
}

/* identical to writing .link:is(:hover, :focus) */
.link {
  &:hover, &:focus {
    outline: 1px solid blue;
  }
}

%interactive {
  &:hover, &:focus {
    outline: 1px solid blue;
  }
}

button {
  @extend %interactive;
}

```

**General Principle**

Code should be easy to predict, easy to find, and have few side-effects. Changes to underlying technologies should not change the outcomes of the code.


### Nesting with at-rules

CSS preprocessors and the browser both allow nested at-rules (e.g. `@media`, `@supports`). This is a useful feature for encapsulating a behavior to a specific selector.

_Avoid_:

* Nested at rules
* Nesting that goes beyond 3 levels

```css
.cardCollection {
  @media screen {
    @media (min-width: 768px) {
      width: 100%;
    }
  }
}

.cardCollection {
  .card {
    .card__header {
      @media (min-width: 768px) {
        width: 100%;
      }
    }
  }
}
```

_Prefer_:

* Flattened at-rules
* Flattened selectors

```css
.cardCollection {
  @media screen and (min-width: 768px) {
    width: 100%;
  }
}

.cardCollection .card .cardHeader {
  @media (min-width: 768px) {
    width: 100%;
  }
}
```

**General Principle**

Code should be easy to understand and predict.

## Acknowledgments [#](#ackowledgements)

Portions of this guide are based heavily on:

* Philip Walton, [CSS Architecture](http://engineering.appfolio.com/2012/11/16/css-architecture/)
* Johnathan Snook, [SMACSS](http://smacss.com/book/)
* Nicolas Gallagher, [About HTML semantics and front-end architecture](http://nicolasgallagher.com/about-html-semantics-front-end-architecture/)
* [Naming things in front-end code](https://blog.frankmtaylor.com/2021/10/21/a-small-guide-for-naming-stuff-in-front-end-code/), Frank M. Taylor

Additional Influence and Resources:

* Nicole Sullivan, [OOCSS](http://coding.smashingmagazine.com/2011/12/12/an-introduction-to-object-oriented-css-oocss/)
* Harry Roberts, [Code Smells in CSS](http://csswizardry.com/2012/11/code-smells-in-css/)
* Harry Roberts, [CSS-Guidelines](https://github.com/csswizardry/CSS-Guidelines#naming-conventions)
* [Block, Element, Modifier](http://bem.info/)

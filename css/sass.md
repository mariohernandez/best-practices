# Sass Architecture & Guidelines (Should be updated for CSS)

- [Coding Conventions](#coding-conventions)
- [Build Process:](#build-process)
	- [Base:](#base)
	- [Variables:](#variables)
	- [Functions and Mixins:](#functions-and-mixins)
	- [Placeholders:](#placeholders)
	- [Default Selectors:](#default-selectors)
- [Sandboxed Development:](#sandboxed-development)

## Coding Conventions

[SMACSS](https://smacss.com/) provides a guideline for separating code into succinct chunks that do fairly simple things well. SMACSS stands for *Scalable and Modular Architecture for CSS*. It's a book and a methodology for writing CSS (created by [Jonathan Snook](http://snook.ca/)), but its most significant and influential aspect is its organizational system, which is designed to provide a set of buckets into which CSS should be organized. The author -- with an emphasis on non-preprocessed CSS -- states that there are 5 fundamental categories for CSS rules: base, layout, module, state, and theme. We agree! There are logical ways to separate CSS rules, but there are even better ways to dynamically structure those rule sets when a preprocessor is used.

[BEM](http://bem.info) – meaning block, element, modifier – is a front-end naming methodology thought up by the guys at [Yandex](http://yandex.ru/). It is a smart way of naming your CSS classes to give them more transparency and meaning to other developers. BEM is a highly useful, powerful, and simple naming convention to make your front-end code easier to read and understand, easier to work with, easier to scale, more robust and explicit and a lot more strict -- all this while keeping the number of selectors on a single HTML DOM element to a minimum. The point of BEM is to tell other developers _more_ about what a piece of markup is doing from its _name alone_. By reading some HTML with some classes in it, you can see how – if at all – the chunks are related; something might just be a _component_, something might be a child, or _element_, of that component, and something might be a variation or _modifier_ of that component. It is easy to see this correlation in the following selector examples:

<table><tr><td>
<pre class="css">.person {}
.hand {}
.female {}
.male {}
.female-hand {}
.male-foot {}
.left-hand {}
.foot-right {}
</pre>
</td><td>
<pre class="scss">.person {}
.person__hand {}
.person__foot {}
.person--female {}
.person--female__hand {}
.person--male__foot {}
.person__hand--left {}
.person--male__foot--right {}
</pre>
</td>
</tr>
  <tr>
    <td>Feral CSS selectors</td>
    <td>Sophisticated BEM selectors</td>
  </tr>
</table>

The plain CSS selectors all make sense, but are somewhat disconnected. Take `.female` for example; female what? What about `.hand`; a clock hand? A hand in a game of cards? By using BEM we can be more _descriptive_ but also a lot more explicit; we tie concrete links to other elements of our code through naming alone. With the BEM-style selectors (on the right above), It should be plain to see that the top-level block is a ‘person’ which has elements, for example, ‘hand’. A person also has variations, such as’ female’ or ‘male’, and that variation in turn has elements and more variations on those elements.

<blockquote align="center">Solid documentation is the physical manifestation of a DRY methodology.</blockquote>

Elemental employs a *Don’t Repeat Yourself* ([DRY](http://thesassway.com/intermediate/using-object-oriented-css-with-sass)) approach to it's implementation. Recognized visual patterns are logically grouped into respective code formats and are documented with [SassDoc](http://sassdoc.com/annotations/); you may say, "isn’t that what the `README` is for?" and you would be correct, but SassDoc ensures that implementation details are available and clearly outlined for all Elemental consumers. Use of the Elemental Component generator will automatically add SassDoc style file comments that include the `@group` annotation already scoped to the local component.

The basic principles of DRY code are:

* Group reusable aspects together (`@functions`, `@mixins`, and `$variables`)

* Name these groups logically (`%placeholders`)

* Add your selectors to the various css groups (`.selector__group--extensions {}`)

<figure align="center"><blockquote>One of the most powerful features of the CSS preprocessor Sass is the mixin, an abstraction of a common pattern into a semantic and reusable chunk. Think of taking the styles for a button and, instead of needing to remember what all of the properties are, having a selector include the styles for the button instead. The button styles are maintained in a single place, making them easy to update and keep consistent.</blockquote><figcaption>Sam Richard | _[DRY-ing Out Your Sass Mixins](http://alistapart.com/article/dry-ing-out-your-sass-mixins)_ - A List Apart</figcaption></figure>

Example: A group of selectors -- pager items for example --  might share similar styles, but also have their own nuances. To achieve this, one could make a series of functions, mixins, and variables that are easily called and assigned to a group of selectors via a placeholder. While compiled rule sets have many selectors attached to them, a clean separation of concerns in the code has been achieved; this structure also makes it easy to leverage certains portions of one component in the development and use of another.

<figure><figcaption><code>.src/styles/elemental-pagination/placeholders/_general.scss</code></figcaption>
<pre class="scss">/// Styles for the parent wrapper
%pager {
  @include set-baseline($pager-baseline...);
  align-items: center;
  display: flex;
  margin: $rhythm-unit 0;
  padding: 0;
}

/// Hide a portion of a pager
%pager--hide {
  @include hidden;
  flex: 0 0 0;
  height: 0;
  margin: {
    bottom: 0;
    top: 0;
  }
  opacity: 0;
  overflow: hidden;
  padding: {
    bottom: 0;
    top: 0;
  }
  visibility: hidden;
}

/// Styles for large sized pager.
%pager--large {
  @include pager-tiny-layout;
  /*max-width: 100%;*/

  @media screen and (min-width: $pager-size-tiny-max-width) {
    @include pager-small-layout;
  }

  @media screen and (min-width: $pager-size-small-max-width) {
    @include pager-medium-layout;
  }

  @media screen and (min-width: $pager-size-medium-max-width) {
    @include pager-large-layout;
  }
}
</pre></figure>

<figure><figcaption><code>./src/styles/elemental-pagination/_mixins.scss</code></figcaption>
<pre class="scss">
/// Styles specific to a tiny pager layout.
@mixin pager-tiny-layout() {
  flex-flow: row wrap;
  %pager--control--first,
  %pager--control--last,
  %pager--item--page {
    @include hidden;
  }

  %pager--item--current {
    display: flex;
  }

  %pager--info,
  %pager--all {
    flex-basis: 100%;
  }
}

/// Styles specific to a small pager layout.
@mixin pager-small-layout() {
  flex-direction: row nowrap;

  %pager--control--first,
  %pager--control--last {
    display: flex;
  }

  %pager--all {
    flex-basis: auto;
  }
}

/// Styles specific to a medium pager layout.
@mixin pager-medium-layout() {
  flex-direction: row;

  %pager--item--page {
    display: flex;
  }

  %pager--info {
    flex-basis: auto;
  }
}

/// Styles specific to a large pager layout.
@mixin pager-large-layout() {
  flex-direction: row;
}
</pre></figure>
<figure><figcaption><code>./src/styles/elemental-pagination/placeholders/variants/_gist.scss</code></figcaption>
<pre class="scss">%pager--gist {
  @extend %pager;
  %pager--info,
  %pager--all,
  [data-el-pager-hide*="gist"] {
    @extend %pager--hide;
  }
}

/// Style for the large/default pager gist variant.
%pager--gist--large {
  @extend %pager--gist;
  @extend %pager--large;
}
</pre></figure>
<figure><figcaption><code>./src/styles/elemental-pagination/default/_variants.scss</code></figcaption>
<pre class="scss">.#{$pager-prefix}--gist {
  @extend %pager--gist;

  &,
  &--large {
    @extend %pager--gist;
    @extend %pager--gist--large;
  }

  &--medium {
    @extend %pager--gist--medium;
  }

  &--small {
    @extend %pager--gist--small;
  }

  &--tiny {
    @extend %pager--gist--tiny;
  }
}
</pre></figure>

## Build Process

[Acquia Gulp](https://github.com/acquia/acquia-gulp) is the default build process for Elemental. The included [Gulp](http://gulpjs.com/) Sass tasks rely on a specific structure that encompasses all of the collected and chosen best practices; there is even an [Elemental Component Yeoman Generator](https://github.com/acquia/generator-elemental-component) that builds this directory structure for you. Each Elemental component contains a `src/` directory that contains images, scripts, and stylesheets that need to be processed in some manner.

Let’s take a look at `./src/styles/`:

<img style="margin: 0.375rem;" align="right" src="https://cloud.githubusercontent.com/assets/1129934/14837423/0ca398f6-0bcd-11e6-9b7c-22aaf688dca6.png" alt="default Elemental Component Sass structure" width="220px">

First, notice that the Sass partials are contained within a directory that matches the name of the component. This is not only in order to avoid collisions with other components and third-party software, but to contain the constructs within this component and make them accessible to other systems by name. This directory could be considered the "module" portion of the SMACSS methodology, kind of. The only direct child Sass file in the styles directory is the component’s primary stylesheet; it aggregates all Sass partials that contain fully qualified selector sets (`_default.scss`) to compile them into one CSS asset.

Sometimes it will make sense to group functions together in their own Sass partials; this is true for all partial includes: variables, placeholders, mixins, etc. To do this, create a directory in `./src/styles/[component-name]/` that matches the file name of the primary partial.

<figure><figcaption><code>./src/styles/elemental-core/_functions.scss</code></figcaption>
<pre class="scss">////
/// Processing functions
///
/// @group core
////
@import 'elemental-core/functions/responsive';
@import 'elemental-core/functions/units';
@import 'elemental-core/functions/rhythm';
@import 'elemental-core/functions/getters';
@import 'elemental-core/functions/color';
@import 'elemental-core/functions/deprecated';
@import 'elemental-core/functions/map-tools';
</pre></figure>

<img style="margin: 0.375rem;" align="right" src="https://cloud.githubusercontent.com/assets/1129934/14837424/0cacd29a-0bcd-11e6-88e5-8331ecc85ef8.png" alt="default Elemental Component Sass structure" width="220px">

### Base

The base file includes component dependencies ([Elemental Core](https://github.com/acquia/elemental-core), which provides the base set of functionality and variables, is a requirement for all components), generates placeholders from those dependencies, and the local component’s partials.

```scss
// Include dependencies.
@import 'elemental-core';

// Generate general style placeholders from elemental-core if they have not
// already been generated.
@include generate-placeholders;

// Include elemental-test libraries.
@import 'elemental-test/variables';
@import 'elemental-test/functions';
@import 'elemental-test/mixins';
@import 'elemental-test/placeholders';
```

Let’s step through the local component’s libraries:

### Variables

While Elemental Core provides a base set of variables to be used by components, it is preferred that local variables are created in each component to allow for easier overrides. Typically, this will look like:

```scss
/// The prefix string to apply to default card identifiers.
$card-prefix: #{$prefix}card !default;

/// The configuration for the general typeface of a card.
$card-type: $type-default !default;

/// Base text color for the card.
$card-text-color: $text-color-base !default;

/// The color to apply to the background of the base card element.
$card-background: $color-monotone-0 !default;

/// The card header link hover background color.
$card-header-background: $color-neutral-2 !default;

/// The card header link hover background color.
$card-header-background-hover: $color-brand-primary-11 !default;

/// Provide spacing from the edges of the card and the elements within.
$card-padding: $rhythm-unit !default;

/// The card border color.
$card-border-color: $color-neutral-3 !default;

/// The card border radius.
$card-border-radius: $border-radius-base !default;

/// The card border width.
$card-border-width: $border-width-base !default;

/// The card shadow color.
$card-shadow-color: $shadow-color !default;

/// The card title text size.
$card-title-text-size: map-get($text-sizes, 0) !default;

/// The card alternate title text size.
$card-title-alternate-text-size: map-get($text-sizes, -1) !default;

/// The card footer background color.
$card-footer-background: $card-header-background !default;

/// The smallest width a card can be.
$card-size-min-width: px-to-relative(280px) !default;

/// Medium size card maximum width.
$card-size-medium-max-width: px-to-relative(600px) !default;

/// Small size card maximum width.
$card-size-small-max-width: px-to-relative(400px) !default;

/// The card error message background color.
$card-message-background-error: $color-state-danger-3;

/// The card info message background color.
$card-message-background-info: $color-brand-primary-1;

/// The card success message background color.
$card-message-background-success: $color-state-success-2;

/// The card warning message background color.
$card-message-background-warning: $color-state-warning-2;
```

While variable names don’t truly use the BEM naming convention’s separators (`__` for elements, `--` for modifiers), they still follow the general pattern. If we look specifically at `$card-message-background-warning`, we can see how the BEM pattern persists; the block is `card`, the element is `message`, a *style-specific* element of message is `background` and the modifier is `warning`, which in this case is a visual state of the previous element, background.

### Functions and Mixins

The Sass functions and mixins that Elemental Core provides are typically enough for a component to achieve the desired form and function. With that stated, should you need to create your own, you can do so in the corresponding Sass partial.

### Placeholders

Placeholders encourage compartmentalized development. One limitation to note about using `@extend` as it applies to placeholder selectors is that it doesn't work between rules in different `@media` blocks.

Consider the following:
```scss
%icon {
  transition: background-color ease 0.2s;
  margin: 0 0.5em;
}

@media screen {
  .error-icon {
    @extend %icon;
  }

  .info-icon {
    @extend %icon;
  }
}
```

This will actually result in a compile error:

```bash
You may not @extend an outer selector from within @media.
You may only @extend selectors within the same directive.
From "@extend %icon" on line 8 of icons.scss
```

There is a very good reason for why it works this way in Sass. Since `@extend` works by adding a selector to another selector without duplicating any of the properties, it's actually impossible to join selectors in different `@media` blocks. However, there is an alternate way of getting this to work: any media query surrounding the placeholder selector will be applied to the selectors extending it providing they are not in a `@media` block of their own:
```scss
@media screen {
  %icon {
    transition: background-color ease 0.2s;
    margin: 0 0.5em;
  }
}

.error-icon {
  @extend %icon;
}

.info-icon {
  @extend %icon;
}
```

### Default Selectors

Default selectors are typically constructed via `%placeholder` extension and the inclusion of `$variables` and `@mixins`.

```scss
.#{$card-prefix} {
  &,
  &--large {
    @extend %card;
    @extend %card--large;
  }

  &--medium {
    @extend %card;
    @extend %card--medium;
  }

  &--small {
    @extend %card;
    @extend %card--small;
  }

  &__header {
    @extend %card--header;
  }

  &__title {
    @extend %card--title;

    &__link {
      @extend %card--title--link;
    }
  }
  ...
}
```

## Sandboxed Development
[Acquia Gulp](https://github.com/acquia/acquia-gulp) -- the build process -- comes with a local development server built-in. You can create a new stylesheet in the`./src/styles/` directory and include that in the `<head>` of the markup test index template. When you’re building the demo pages for the component you may also need to require styles from other components (EX: elemental-baseline, -iconography, etc); when this need arises, you should avoid including those components in the final output stylesheet by only including them at the top of your test stylesheet.

The typical setup for this looks something like:
*Download dev-dependencies with bower:*
<figure><figcaption>Elemental Card's <code>./bower.json</code></figcaption>
<pre class="json">{
  "name": "elemental-card",
  ...
  "dependencies": {
    "elemental-core": "git@github.com:acquia/elemental-core.git#~v0.0.15"
  },
  "devDependencies": {
    "elemental-baseline": "git@github.com:acquia/elemental-baseline.git#~v0.0.6",
    "elemental-iconography": "git@github.com:acquia/elemental-iconography.git#~v0.0.3",
    "svg4everybody": "~2.0.0"
  }
}
</pre></figure>

*Import dev-dependencies into test stylesheet:*
<figure><figcaption>Elemental Card's <code>./src/styles/test.scss</code></figcaption>
<pre class="scss">@import 'elemental-baseline';
@import 'elemental-iconography';
@import 'elemental-card/base';

/// $variables, %placeholders, @mixins, @functions, and .selectors in this file are all
/// specific to the test markup pages and should not be leveraged when compiling the
/// distributed component stylesheet(s)
</pre></figure>

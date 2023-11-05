
# Code maintenance

## Goals

Code changes over time. And like any other system, it is prone to entropy. It should be the goal of a project to acknowledge code entropy and to mitigate it through good hygiene and consistent maintenance practices.

## Contents

* Goals
* [General Maintenance](#general-maintenance)
  * [`TODO` Comments](#todo-comments)
  * [`SHAME` Comments](#shame-comments)
  * [Linter override Comments](#linter-override-comments)
  * [`SOLUTIONS` Comments](#solutions-comments)
  * [`OUTPUT` Comments](#output-comments)
  * [Compiled Comments](#compiled-comments)
  * [Comments in General](#comments-in-general)
* [Deprecation](#deprecation)
* [Refactoring](#refactoring)
* [Shame File](#shame-file)


## General Maintenance

### `TODO` Comments

_Avoid_:

* Mixed casing, or leaving out the `@` symbol
* `TODO` comments without specific, actionable work
* Approving Merge Requests / pull requests where `TODO` comments don't include Jira tickets

```css
.menu .link {
    color: #00ff00; /* TODO: this isn't a variable*/
}

```

_Prefer_:

* Writing `@TODO` all uppercase, with a preceding `@`
* Creating a Jira ticket and including the ticket number with the `@TODO` comment
* Writing a `TODO` in such a way that it could work with Todo Tree, Todo Highlight, or other editor extensions

```css

.menu .link {
    color: #00ff00; /* @TODO: Make color values variables. See JIRA-1234 */
}

```

**General Principle**

TODO comments should be easy to find, easy to spot, and contain actionable work that can prioritized in the development lifecycle.

### `SHAME` Comments

_Avoid_:

* Mixed casing, or leaving out the `@` symbol (see [TODO Comments](#todo-comments))
* `Shame` comments without specific, actionable work

```css

.menu .link {
    color: #00ff00 !important; /* shame */
}

```

_Prefer_:

* writing `@SHAME` all uppercase, with a preceding `@`
* Describing the problem with the code, and how it can be improved
* Potentially moving the code and comment to a shame file (see [Shame File](#shame-file))

```css

```css
.menu .link {
    color: #00ff00 !important;
    /* @SHAME: this is because `menu a` has !important, too. Don't try to change. We need to redo specificity once we figure out how. */
}

```

**General Principle**

Shame comments should be easy to find, easy to spot, and explain why it couldn't be a TODO.

### Linter override comments

_Avoid_:

* Overwriting multiple linter rules at the top of a file
* Overwriting linter rules without an explanation

```javascript
async function asyncForEach(array, callback) {
 for (let index = 0; index < array.length; index += 1) {
  // eslint-disable-next-line no-await-in-loop
  await callback(array[index], index, array);
 }
}
```

_Prefer_:

* Overwriting linter rules on a per-line basis
* Justifying why a linter rule is being overwritten
* Updating the lint config if a rule doesn't make sense or if a rule is always being overwritten

```javascript
async function asyncForEach(array, callback) {
 for (let index = 0; index < array.length; index += 1) {
  // the whole point of this function is to be an asynchronous forEach
  // eslint-disable-next-line no-await-in-loop
  await callback(array[index], index, array);
 }
}
```

**General Principle**

Show that you understand the linter rule by giving an explanation for why it shouldn't apply in this case.

### `SOLUTIONS` Comments

_Avoid_:

* Code that is written because of a technological limitation &mdash; without explaining the problem

_Prefer_:

* Listing out the problem and potential solutions, should libraries or frameworks be updated

```scss
/* @SOLUTIONS  This is because Sass doesn't have namespaces or modules.
If it ever does, move all these variables to a separate file and import them.
Or figure out how to house all the dark variables under some namespace.
*/
$componentStyles: (
  'dark': (
    'foregroundColor': var(--rhd-theme--component-font--color-light),
    'backgroundColor': var(--rhd-theme--component-background--color-dark),
    'cardColor': var(--rhd-theme--component-background--color-dark),
  )
);
```

### `OUTPUT` Comments

_Avoid_:

* Doing things nebulously
* Not describing what the output of compiled/dynamic code is

```scss
.card {
  @include e('header') {
    color: #ff0000;
    text-decoration: underline;
  }

  @include m('primary') {
    font-weight: bold;
    @include e('footer') {
        border: 1px solid #000000;
    }
  }
}
```

_Prefer_:

* `@OUTPUT` comments that describe what the output of any generated/dynamic/compiled code is

```scss
/* @OUTPUT  This uses BEM mixins to generate the following classes:
.card__header
.card--primary
.card--primary__footer
*/
.card {
  @include e('header') {
    color: #ff0000;
    text-decoration: underline;
  }

  @include m('primary') {
    font-weight: bold;
    @include e('footer') {
        border: 1px solid #000000;
    }
  }
}

```

**General Principle**

If you're the only one who knows what the code will do, it's not maintainable. Explain what the code does and make that outcome discoverable.

### Compiled Comments
Some compilers (like SASS) will compile comments into the final CSS. This can be useful for documenting code, but it can also be a source of frustration if a compiled comment exposes information that isn't necessary to the front-end.

_Avoid_:

* Compiled comments that expose information that isn't necessary to the front-end

```scss
/*
* This is a mixin for BEM that generates "class__element"
* @param {string} $element - The element name
*/
@mixin element($element) {
  &__#{$element} {
      @content;
  }
}
```

_Prefer_:

* Compiled comments that are useful only at compile time

```scss
/// This is a mixin for BEM that generates "class__element"
/// @param {string} $element - The element name
@mixin element($element) {
  &__#{$element} {
      @content;
  }
}
```

**General Principle**

Leave the right comment in the right place.

### Comments in general

_Avoid_:

* Writing names of you or other developers in code
* Comments that don't add value
* Comments that are 'vestigial' (left over from a previous iteration of the code)

_Prefer_:

* Comments that give explanations for why a piece of code is written a certain way
* Comments that provide guidance for how to use code

## Deprecation

It's perfectly natural that code reaches an end-of-life. When this happens, always favor removing that code over leaving it in the codebase. What's hard about deprecation isn't identifying old code, but actually deleting it.

Here we outline a process that should make deprecation a process that can be followed consistently across projects. This process involves three basic principles:

* Identify the code
* Communicate with the team
* Remove the code


0. Create ticket that outlines the code that will be deprecated.
    * Note: This is for communication and impact analysis
    * The ticket should contain analysis of impact, reasons for deprecation
    * Developers / BAs, stakeholders are notified through the ticket
1. Comment code with a `@TODO`
     * Note: this is not commenting-out the code.
     * Reference the ticket created in step 0.
     * Once a commit and PR with this todo is merged, ticket in step 0 is finished.
2. Create removal ticket after approval is given
     * Ticket should refer to previous one with the analysis
3. Comment-out code
    * Note: We comment out the code (not delete it) for QE / Regression:
        * it's easier to uncomment code than revert a commit
    * Note: this may not be possible if deprecating a whole file or library.
        * in this case consider removing the file from the build process
    * Comment with `@DEPRECATED`
    * Reference the ticket number
    * Provide deadline for removing the commented out code
4. On a scheduled basis, remove code tagged with `@DEPRECATED`
    * Sprintly
    * Quarterly
    * Yearly

**General Principles**

Communicate early and often that code is being deprecated. Have a plan for removing the code before you do it, and for undoing it if problems should arise. And review your codebase regularly.

Step 3 may not make sense for the codebase or the code. Regardless, consider how you would undo the deprecation if it were to cause problems.

## Refactoring
Sometimes code doesn't reach an end-of-life, but instead needs a facelift. This is where refactoring comes in. Refactoring is the process of changing the structure of code without changing its behavior.

Here we outline a process that should make deprecation a process that can be followed consistently across projects. This process involves three basic principles:

* Identify the code
* Communicate with the team
* Improve the code

0. Create ticket that outlines the code that will be refactored.
    * Note: This is for communication and impact analysis. It may be called a "spike" ticket because it's just for investigation.
    * The ticket should contain analysis of impact, reasons for refactor
    * Developers / BAs, stakeholders are notified through the ticket
    * Learn and document what tests rely on this refactored code
1. Comment code with a `@TODO`
     * Reference the ticket created in step 0.
     * Once a commit and PR with this todo is merged, ticket in step 0 is finished.
2. Create refactoring ticket after approval is given
     * Ticket should refer to previous one with the analysis
3. Refactor the code code
    * Write regression testing guidance FIRST
    * rewrite the code in an appropriately named branch
    * Add a `@REFACTORED` comment, pointing to the ticket, so that you can inform future developers that the code had a previous life
4. Merge the refactored code

**General Principles**
Communicate early and often that code is being refactored. Know how to test your changes before you make them. Make sure others know how code was changed.

## Shame File

A [known practice is to use a shame.css file](https://csswizardry.com/2013/04/shame-css/) that's dedicated to quick fixes and "hacks". In an ideal world we would never have quick fixes or hacks. But so long as we live in a real world, we will, and therefore we should have a process safely fixing and hacking.

*Avoid*:

* More than one shame file per project
* Adding rules to a shame file without explanation
* Forever growing a shame file

*Prefer*:

* Only one Shame file per project
* The shame file should come at the end of the cascade
* The shame file should have a lengthy header comment at the top that explicitly explains its purpose
* Shame file rules are never meant to be permanent
* Every shame file rule should have a path for leaving the file:
  1. Put a `@TODO` comment with the following
      * `@TODO` contains the part of the codebase the fix is related to
      * `@TODO` explains why it was needed and had to be placed in shame
      * `@TODO` explains a more permanent solution
  2. Create a ticket number and reference it in the `@TODO`
  3. Add the ticket to the backlog
* Deny any peer review / merge request that contains a shame file entry without
  * a `@TODO` comment
  * a ticket number

*General Principle*

Fixes and hacks are inevitable, not permanent. Always have a plan for removing them.

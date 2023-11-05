# CSS formatting

## Terminology

For those unfamiliar with [CSS terminology](http://www.w3.org/TR/CSS2/syndata.html#statements), these are the concise terms used in these standards.

```css
selector {
  property: value;
}
```

A **rule set** (also called a **rule**) consists of a selector followed by a declaration block.

The **selector** consists of everything up to (but not including) the first left curly brace (```{```).

A **declaration block** starts with a left curly brace (```{```) and ends with the matching right curly brace (```}```). In between there must be a list of zero or more semicolon-separated (```;```) declarations.

A **declaration** consists of a **property name**, followed by a colon (```:```), followed by a **value**, ended with a semi-colon (```;```) if the following line has yet another property definition.

## Whitespace

### Indentation

Do **not** use tabs for indentation. They lead to inconsistent display of the source code, since many text editors and most text viewers (like web browsers) cannot have their “tab size” configured.

Use **2 spaces** for each level of indentation, the same standard as Drupal’s PHP and JavaScript code.

*   Declarations (property/value pairs) should be indented one level relative to their selector.
*   Rulesets within a media block or a media query should be indented one level relative to the media statement.
*   Comments should be indented the same amount as the declaration or ruleset they describe.

```css
@media print {
  /* This line is indented with 2 spaces, 2 spaces x 1 level of indentation. */
  .example {
    /* This line is indented with 4 spaces, 2 spaces x 2 levels of indentation. */
    padding: 0;
  }
}
```

### Blank lines

*   In general, do **NOT** separate each ruleset by a blank line.
*   If a ruleset has a proceeding Doxygen-style or single-line-style comment that describes it, place a blank line before the comment.
*   If two rulesets have no interleaving blank line, they must be logically related. If they are not logically related to each other, add a blank line and a comment describing the second ruleset.

```css
/* A comment describing the ruleset. */
.selector-1,
.selector-2,
.selector-3[type="text"] {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  display: block;
  margin: 0;
  font-family: Times, "Times New Roman", sans-serif;
  color: #333;
  background: #fff;
  background: linear-gradient(#fff, rgba(0, 0, 0, 0.8));
}

/**
 * A longer comment describing this ruleset. Note
 * the blank line before the docblock.
 */
.selector-4,
.selector-5 {
  background-color: lime;
}

/* This logical grouping of rulesets has no interleaving blank lines. */
.profile {
  margin: 16px 0;
  margin: 1rem 0;
}
.profile__picture {
  float: right; /* LTR */
}
```

### Line endings

There MUST NOT be any whitespace (spaces or tabs) at the end of lines. This means blank lines should also not contain any spaces or tabs. Inconsistent trailing whitespace can add lines to diffs/patches and makes changes harder to notice.

All text files should end with a single blank line. This makes git commits easier to read since it's clearer what is being changed when lines are added to the end of a file and it avoids the verbose ```\ No newline at end of file``` warning in patches.

Files should be formatted with Unix line endings (a newline character, denoted as ```\n``` or ```LF```), which is also the default in Mac OS X. Do not use Windows line endings (a carriage return plus a newline, denoted as ```\r\n``` or ```CRLF```).

Tip: configure your editor to “show invisibles”. This will allow you to eliminate end-of-line whitespace, eliminate unintended blank-line whitespace, and avoid polluting commits.

Drupal 8 includes an [EditorConfig](http://editorconfig.org/) file in [its root directory](http://drupalcode.org/project/drupal.git/tree/refs/heads/8.x) to help maintain these whitespace conventions.

## Comments

Well commented code is extremely important. Take time to describe components, how they work, their limitations, and the way they are constructed. Don't leave others guessing as to the purpose of uncommon or non-obvious code.

While contributed code remains consistent with the rest of Drupal's code base, borrowing some of the CSS comment styles from the [Doxygen and comment formatting conventions](http://drupal.org/node/1354) for PHP files, Elemental uses a slightly different syntax for documenting comments. [SassDoc Annotations](http://sassdoc.com/annotations/) are used in Elemental's Sass files in order to present documentation in a meaningful way. These annotations require an extended version (```///```) of C++ comments - each line of a documenting code block needs to start with three forward slashes. SassDoc will not parse any C style (```/* */```) comments, so feel free to include both styles in your files, just know the latter will be output in the resulting CSS file(s).

### File comments

Each file should start with a comment describing what the file does. For example any CSS file in the Drupal code base starts with somthing like:

```css
/**
 * @file
 * Short description describing the file.
 *
 * The first sentence of the long description starts here and continues on this
 * line for a while finally concluding here at the end of this paragraph.
 */
```

SassDoc's [File Level Annotations](http://sassdoc.com/file-level-annotations/) require 4 forward slashes (```////```) at the beginning and the end of the comment block, like so:

```css
////
/// Short description describing the file.
///
/// The first sentence of the long description starts here and continues on this
/// line for a while finally concluding here at the end of this paragraph.
///
/// @group api-docs
/// @author Joan Smith
////
```

Note: a blank line should follow a file comment. Keep line-lengths to 80 columns, when possible. For more information, see the [PHP file comment standards](http://drupal.org/node/1354#files).

### Multi-line comments

When describing a ruleset or set of rulesets, any comment that requires 2 or more lines (wrapped to 80 characters) must follow the Doxygen comment style (also called a “docblock”).

```css
/**
 * Short description using Doxygen-style comment format.
 *
 * The first sentence of the long description starts here and continues on this
 * line for a while finally concluding here at the end of this paragraph.
 *
 * The long description is ideal for more detailed explanations and
 * documentation. It can include example HTML, URLs, or any other information
 * that is deemed necessary or useful.
 */
.example-rule {
}
```

Place the comment on the line immediately above the ruleset (or rulesets) it describes. Place a blank line before the docblock comment. See the [Doxygen and comment formatting conventions](http://drupal.org/node/1354) for more info.

### Single-line comments

When describing a property or ruleset, any comment that can be written inside the 80 character line length limit can use a simple CSS comment style.

```css
.example {
  /* Override the default margins. */
  margin: 0;
}

/* This is a variant of the .example component */
.example--item {
  display: inline;
}
```

Place the comment on the line immediately above the property or ruleset it describes. The comment should be indented the same amount as the property or ruleset it describes.

If the comment is describing a ruleset, place a blank line before the comment.

### Styling for Right-To-Left Languages

It is common for RTL language websites to have their designs flipped in the left/right direction. For direction specific property/values, add the comment ```/* LTR */``` on the same line preceded by a single space. In [Drupal 6 and 7](https://drupal.org/node/222782), the inclusion of a separate RTL stylesheet is automated. In Drupal 8, follow with an additional ruleset containing the inverse property/values, beginning with the attribute selector [dir="rtl"].

###### Example Rulesets for Drupal 6 and Drupal 7

```css
[example.css]

.example-rule {
  float: left; /* LTR */
  margin-right: 24px;
  margin-right: 1.5rem; /* LTR */
  padding: 0 4px;
  padding: 0 0.25rem;
}
```

```css
[example-rtl.css]

.example-rule {
  float: right;
  margin-left: 24px;
  margin-left: 1.5rem;
  margin-right: 0;
}
```

###### Example Rulesets for Drupal 8

```css
[example.css]

.example-rule {
  float: left; /* LTR */
  margin-right: 24px;
  margin-right: 1.5rem; /* LTR */
  padding: 0 4px;
  padding: 0 0.25rem;
}
[dir="rtl"] .example-rule {
  float: right;
  margin-right: 24px
  margin-left: 1.5rem;
  margin-right: 0;
}
```

*   when you use the keywords, 'left' or 'right' in a property, e.g. ```float: left;```
*   where you use unequal margin, padding or borders on the sides of a box, e.g. ```margin-left: 1rem;``` or ```padding: 0 0 0 2rem;```
*   where you specify the direction of the language, e.g. ```direction: ltr;```

## Format

Our CSS formatting ensures the code is easy to read, easy to clearly comment, minimizes the chance of accidentally introducing errors, and results in useful Git diffs and blames.

### Rulesets

*   Use one selector per line when a ruleset has a group of selectors separated by commas.
*   The opening brace (```{```) of a ruleset’s declaration block should be on the same line as the selector (or the same line as the last selector in a group of selectors.) The opening brace should include a single space before it.
*   Place the closing brace (```}```) of a ruleset in the same column as the first character in the selector of the ruleset.
*   Include one declaration per line in a declaration block.
*   Each declaration should be indented one level relative to its selector.

###### Example Ruleset

```css
.selector-alpha,
.selector-beta {
  counter-reset: section;
  text-transform: small-caps;
}
```

### Properties

*   In a declaration, the property name should be immediately followed by a colon, then a single space, and then the property’s value.
*   Include a semi-colon at the end of all declarations, including the last declaration in a declaration block.
*   When hex values are used for colors, use lowercase and, if possible, the shorthand syntax, e.g. ```#aaa```. Colors may be expressed with any valid CSS value, such as hex value, color keyword, rgb() or rgba(). Note that IE8 does not support all color syntaxes and will require a fallback value.
*   For property values that require quotes, use double quotes instead of single quotes, e.g. ```font-family: "Arial Black", Arial, sans-serif;``` and ```content: " ";```.
*   If a property does not require quotes (e.g. ```url()```, do not add them. This means ```background-image: url(path/image.png)``` instead of ```background-image: url("path/image.png")```
*   Use rem units preceded by px units for a safe fallback, unless it creates an undesired effect.
*   Quote attribute values in selectors, e.g. ```input[type="checkbox"]```.
*   Where allowed, avoid specifying units for zero-values, e.g. use ```margin: 0;``` instead of ```margin: 0px;```.
*   Include a space after each comma in comma-separated property or function values.

#### Example properties

| property: value;                 | Notes                                     |
|----------------------------------|-------------------------------------------|
| `display: block;`                | Basic syntax  |
| `color: #fff;`                   | Use shorthand syntax for hexadecimal colors when possible |
| `color: #df7dcf;`                | Always use lowercase |
| `font-family: "Frutiger Ultra";` | Use double quotes instead of single quotes |
| `text-shadow: 0 0 2px #ddd;`     | Do not attach units to zero-values |
| `font-size: 24px;`               | |
| `font-size: 1.5rem;`             | Use rem units preceded by px units for a safe fallback, unless it creates an undesired effect. |
| `color: rgba(0, 136, 18, 0.8);`  | Spaces MUST follow commas in property or function values |

### Declaration order

The declarations in a ruleset should be ordered so that the purpose of the declaration block is most obvious. Clarity should be the guiding principle. We can help to achieve this goal by placing structurally important properties before others: positioning, box model, then other properties.

1.  Positioning properties include: ```position```, ```float```, ```clear```, ```top```, ```right```, ```bottom```, ```left```, ```direction```, and ```z-index```.
2.  Box model properties include: ```display```, ```[(max|min)-]height```, ```[(max|min)-]width```, ```margin```, ```padding```, ```border``` and their various longhand forms (```margin-top```, etc.) Plus ```box-sizing```.
3.  Other declarations.

Within each of the above groups, properties can be grouped alphabetically or grouped with like properties next to each other, e.g. putting font and text properties next to each other. Drupal’s coding standards are purposefully vague here because there is no consensus on this issue (as of 2013), but we respect each other’s abilities and preferences.

Vendor prefixed properties should be directly before their non-prefixed version. This allows the official version of the property to override any inconsistencies in the vendor-prefixed versions once those browsers implement the official property. If browser bugs or cross-browser issues necessitate any deviation from this ordering, it should be clearly documented.

Again, the order of properties is meant to reinforce the purpose of the ruleset. As such, **it is much more important to add comments to the ruleset then to worry about property ordering.**

```css
.selector {
  /* Positioning declarations */
  position: absolute;
  top: 0;
  left: 0;
  z-index: 10;
  /* Box model declarations */
  display: inline-block;
  width: 100%;
  padding: 10px;
  padding: 0.625rem;
  border: 1px solid #333;
  /* Other declarations */
  background: #000;
  color: #fff;
  font-family: sans-serif;
  font-size: 18px;
  font-size: 1.125rem;
}
```

### Exceptions and slight deviations

Large blocks of single declarations can use a slightly different, single-line format. In this case, a space should be included after the opening brace and before the closing brace.

```css
.selector-1 { width: 10%; }
.selector-2 { width: 20%; }
.selector-3 { width: 30%; }
```

Long, comma-separated property values—such as collections of gradients or shadows—can be arranged across multiple lines in an effort to improve readability and produce more useful diffs.

```css
.selector {
  background-image:
    linear-gradient(#fff, #ccc),
    linear-gradient(#f3c, #4ec);
  box-shadow:
    1px 1px 1px #000,
    2px 2px 1px 1px #ccc inset;
}
```

### Media Queries

Media queries should be written in the same style as ruleset. Any containing rulesets are indented by two spaces.

*   One space between the media feature and the value.
*   All values to be written in rems unless it is inappropriate.
*   Add the pixel value in a comment directly after the the opening brace.

```css
@media screen and (min-width: 28.125rem) { /* 450px */
  #page {
    margin-left: 20px;
    margin-left: 1.25rem;
    margin-right: 20px;
    margin-right: 1.25rem;
  }
}
```

## Miscellaneous

### @charset statements

Character set statements (like ```@charset "UTF-8";```) are only valid if they are at the very top of a CSS file. Since Drupal’s CSS aggregator combines multiple CSS files into one file, Drupal will strip all ```@charset``` statements so that the aggregated file remains valid CSS.

This means CSS files **MUST NOT** include any ```@charset``` statements. The default encoding for CSS files is UTF-8. Any CSS comments or ```content``` property values MUST be encoded with UTF-8.

## Practical example

An example of various conventions.

```css
////
/// Layouts for this theme
/// @group layout
/// @author Joan Smith
////

/**
 * @file /path/to/theme/file.ext
 * Layouts for this theme.
 */

/**
 * Column layout with horizontal scroll.
 *
 * This creates a single row of full-height, non-wrapping columns that can be
 * browsed horizontally within their parent.
 */

/// Column Layout with horizontal scroll
///
/// This creates a single row of full-height, non-wrapping columns that can be
/// browsed horizontally within their parent.
///
/// @example html
///   <div class="grid">
///     <div class="cell cell-3"></div>
///     <div class="cell cell-3"></div>
///     <div class="cell cell-3"></div>
///   </div>

/**
 * Grid container
 *
 * Must only contain '.cell' children.
 */
.grid {
  height: 100%;
  /* Remove inter-cell whitespace */
  font-size: 0;
  /* Prevent inline-block cells wrapping */
  white-space: nowrap;
}

/**
 * Grid cells
 *
 * No explicit width by default. Extend with '.cell-n' classes.
 */
.cell {
  position: relative;
  display: inline-block;
  overflow: hidden;
  box-sizing: border-box;
  height: 100%;
  /* Set the inter-cell spacing */
  padding: 0 10px;
  padding: 0.625rem;
  border: 2px solid #333;
  vertical-align: top;
  /* Reset white-space */
  white-space: normal;
  /* Reset font-size */
  font-size: 16px;
  font-size: 1rem;
}

/* Cell states */
.cell.is-animating {
  background-color: #fffdec;
}

/* Cell dimensions */
.cell-1 { width: 10%; }
.cell-2 { width: 20%; }
.cell-3 { width: 30%; }
.cell-4 { width: 40%; }
.cell-5 { width: 50%; }

/* Cell modifiers */
.cell--detail,
.cell--important {
  border-width: 4px;
}
```

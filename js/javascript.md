# JavaScript coding standards

## Indenting

* All code MUST indent using two (2) space characters,
* All code MUST NOT indent using tab characters,
* All code MUST NOT end with trailing whitespace.

## Semi-colons

JavaScript allows optional "semi-colon insertion". Drupal standards do not.

* All statements (except ```for, function, if, switch, try, while```) MUST be followed by a semi-colon (```;```),
* Return values MUST start on the same line as the ```return``` keyword.

**EXCEPTIONS:**

* Anonymous functions assigned to a variable MUST be followed by a semi-colon.

```js
Drupal.behaviors.tableSelect = function (context) {
  // Statements...
};
```

* while control structures MUST be followed by a semi-colon

```js
do {
  // Statements...
} while (condition);

```

## File-closure and Strict mode

All JavaScript code MUST be declared inside a closure wrapping the whole file and this closure MUST be in strict mode.

```js
/**
 * @file
 */
(function () {

  "use strict";

  // All the JavaScript for this file.

})();
```

## CamelCasing

For variables that are not [constants](#constants) or [constructors](#constructors), multi-word variables and functions SHOULD be lowerCamelCased.

The first letter of each variable or function SHOULD be lowercase, and the first letter of subsequent words SHOULD be capitalized.  There SHOULD NOT be underscores between the words.

In case a variable contains a jQuery object, the variable MUST start with a dollar sign (`$`):

```js
var $form = $('#search-block-form');
var $inputs = $form.find('input');
```

## Variables and Arrays

Due to enabled strict mode, an undeclared variable will halt the script, and on old browsers such variables are implicitly exported into global scope.

All variables MUST be declared with `var` before they are used and SHOULD be declared only once. All variables SHOULD be declared at the beginning of a function.

Each variable assignment SHOULD be declared on a separate line.  Variables that are only declared but do not get a value assigned MAY be declared on the same line.

```js
var anArray = [];
var eventCallback = function () {};
var curTableDragSetting, curTableDragIndex;
```

### Global Variables

@TODO

### Constants

Pre-defined constants SHOULD be all-upercase and words separated by underscores: `UPPER_UNDERSCORED`.

Variables added via PHP SHOULD be [lowerCamelCased](#camelcasing), so that they are consistent with other JavaScript variables:

```js
<?php
  $element['#attached']['js'][] = array(
    'data' => array('myModule' => array('basePath' => base_path())),
    'type' => 'setting',
    );
?>
```

This variable would then be referenced:

```js
  Drupal.settings.myModule.basePath;
```

### Arrays

Arrays SHOULD be formatted with one space separating each element and the assignment operator, if applicable:

```js
var someArray = ['hello', 'world'];
```

If the line is longer than 80 characters, each element SHOULD be broken into its own line, and indented one level.

Unlike PHP, there MUST NOT be a trailing comma at the end of the last array element.

## Typeof

In type comparisons, the value tested MUST NOT be wrapped in parenthesis.

```js
if (typeof myVariable === 'string') {
  // ...
}
```

## Functions

### Function and method names

Function names SHOULD begin with the name of the module or theme declaring the function, so as to avoid name collisions.

```js
Drupal.behaviors.tableDrag = function (context) {
  Object.keys(Drupal.settings.tableDrag).forEach(function (base) {
    $('#' + base).once('tabledrag', addBehavior);
  });
};
```

### Function Declarations

* The ```function``` keyword MUST be followed by one space.
* Named functions MUST NOT have a space between the function name and the following left parenthesis.
* Optional arguments (using default values) SHOULD be defined at the end of the function signature.
* Every function SHOULD attempt to return a meaningful value.

```js
Drupal.behaviors.tableDrag = function (context) {
  // ...
  this.clickCallback = function (e) {
    return false;
  };
  // ...
};

function funStuff(field, settings) {
  var settings = settings || Drupal.settings;
  alert("This JS file does fun message popups.");
  return field;
}

// Anonymous:
function () {}

// Named:
function closeDialog() {}
```

Note: The above examples code are lacking JSDoc comments, only for clarity.

### Function Calls

Functions SHOULD be called with no spaces between the function name, the opening parenthesis, and the first parameter.

The SHOULD be one space between commas and each parameter, and there SHOULD NOT be a space between the last parameter, the closing parenthesis, and the semicolon.

```js
var foobar = foo(bar, baz, quux);
```

There SHOULD be one space on either side of an equals sign used to assign the return value of a function to a variable.

## Constructors

Constructors are functions that are designed to be used with the `new` prefix.  The `new` prefix creates a new object based on the function's prototype, and binds that object to the function's implied this parameter.  JavaScript doesn't issue compile-time warning or run-time warnings if a required `new` is omitted.  If you do not use the `new` prefix, no new object will be made and operations will be bound to the global object instead.

Constructor functions MUST be given names with an initial uppercase character.

A function with an initial uppercase name MUST NOT be called without a `new` operator.

```js
function CollapsibleDetails(node) {}

var collapsibleDetail = new CollapsibleDetails(element);
```

## Comments

Inline documentation for source files MUST follow the [JavaScript API documentation and comment standards](https://drupal.org/node/2183405) (based on JSDoc).

Non-JSDoc comments are strongly RECOMMENDED.

A general rule of thumb is that if you look at a section of code and think "Wow, I don't want to try and describe that", you SHOULD comment it before you forget how it works. Comments MAY be removed by JS compression utilities later, so they don't negatively impact the file download size.

Non-JSDoc comments SHOULD use capitalized sentences with punctuation. Comments SHOULD be on a separate line, immediately before the code line or block they reference.

```js
// Unselect all other checkboxes.
doSomething();
```

If each line of a list needs a separate comment, comments MAY be placed on the same line and MAY be formatted to a uniform indent for readability.

C style comments (```/* */```) and standard C++ comments (```//```) are both allowed.

## String Concatenation

Expressions SHOULD be separated with one space before and after the `+` operator to improve readability.

```js
var string = 'Foo' + bar;
string = bar + 'foo';
string = bar() + 'foo';
string = 'foo' + 'bar';
```

The concatenating assignment operator (`+=`) SHOULD be separated with one space on each side as with the assignment operator:

```js
var string += 'Foo';
string += bar;
string += baz();
```

## Control Structures

### if

For ```if``` statements:

```js
if (condition1 || condition2) {
  action1();
}
else if (condition3 && condition4) {
  action2();
}
else {
  defaultAction();
}
```

Control statements MUST have one space between the control keyword and opening parenthesis, to distinguish them from function calls.

Control structures MUST always use curly braces, even in situations where they are optional. Having them increases readability and decreases the likelihood of logic errors being introduced when new lines are added.

### switch

For ```switch``` statements:

```js
switch (condition) {
  case 1:
  action1();
  break;

  case 2:
  action2();
  break;

  default:
  defaultAction();
}
```

### try

For ```try/catch``` statements:

```js
try {
  // Statements...
}
catch (error) {
  // Error handling...
}
finally {
  // Statements...
}
```

### for in

The body of every ```for in``` statement MUST be wrapped in an ```if``` statement that performs the filtering. It MAY select for a particular type or range of values, or it can exclude functions, or it can exclude properties from the prototype.

```js
for (var variable in object) {
  if (filter) {
    // Statements...
  }
}
```

You MUST use the ```hasOwnProperty``` method to distinguish the true members of the object, which SHOULD be placed inside the loop, not on the same line:

```js
for (var variable in object) {
  if (object.hasOwnProperty(variable)) {
    // Statements...
  }
}
```

## Operators

### Comparisons

The ```==``` and ```!=``` operators do type coercion before comparing. This can lead to unexpected errors.

Strict equality MUST be used in comparisons (```===``` or ```!==```).

### Comma Operator

You SHOULD NOT use the comma operator, with the exception of the control part in `for` statements.

The comma operator causes the expressions on either side of it to be executed in left-to-right order, and returns the value of the expression on the right.

```js
var x = (y = 3, z = 9);
```

This sets `x` to `9`.  This can be confusing for users not familiar with the syntax and makes the code more difficult to read and understand.

## TL;DR — Too Long Didn’t Read

Many of the coding standards that Elemental adheres to are directly from the [Drupal](http://drupal.org) OpenSource Project. You're going to have to read the documentation if you want to contribute back to Elemental. Take a few minutes to look at the source of our standards coding standards, if you'll even read 'em that is.

*  [JavaScript coding standards](https://www.drupal.org/node/172169)
*  [CSS coding standards]()

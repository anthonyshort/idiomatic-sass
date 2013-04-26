# Principles of writing idiomatic Sass

While many people think of Sass as just CSS there are many unique challenges that 
need to be addressed if we want to be able to write the best possible code. In
this document I'll go through some coding styles that will help you share code
with others and avoid a common pitfalls.

In addition to many of these principles you should read [Idiomatic CSS](https://github.com/necolas/idiomatic-css)
and apply standard CSS best-practices along with these. Understanding how to write
great CSS is the foundation this is built upon. For this reason, there will be a
bit of overlap.

## Table of Contents

1. [Object-Oriented Sass](#oosass)
2. [Naming Conventions](#naming-conventions)
3. [Selectors](#selectors)
4. [Properties](#properties)
   * [Ordering](#ordering)
5. [Nesting](#nesting)
6. [Indentation](#indentation)
7. [File Structure](#file-structure)
8. [Functions](#functions)
9. [Mixins](#mixins)
10. [Modules](#modulespackages)
   * [File Structure](#file-structure-1)
   * [Namespacing](#namespacing)
   * [Module Entry Point](#module-entry-point)
   * [Package Management](#package-management)
   * [Load Paths](#load-paths)
   * [Dependencies](#dependencies)
11. [Definitions](#definitions)

## Object-Oriented Sass

Writing good Sass code starts with correctly dividing and modularizing your objects. It is arguably more
important than any other aspect of writing CSS.

Well-written Sass is:

### Decoupled

Objects should never manipulate other objects. eg. `.message` would never change the style of a nested object 
called `.list`. Instead use child selectors like `.message__list` and use both classes in the markup 
`<div class="list message__list">` or use a modifier `<div class="message"><div class="list list--small"></div></div>`

### Specific

Break functionality into smaller objects. Each object should do one thing and do it well.

### Not location-based

Never, ever use location-based styling. This means a block is never styled different because it is within another block. Objects should have "modifiers" instead of location-related styles `.block--large {}` instead of `#sidebar .block {}`

### Never uses IDs

Yep, never. You don't need them and they aren't re-usable by nature

### Separates layout from style. 

This means an object that handles background and border won't control padding and margin. 
Styles generally fall into a couple of categories: layout, texture, typography. Each object 
should generally only handle one of these. But be pragmatic about it and consider reusability at all times.

Enforce these rules by using one of the naming conventions in the next section.

## Naming Conventions

It's important that you use a consistent naming convention for your selectors. For this,
I recommend looking at the [BEM](http://bem.info) or [Montage](http://montagejs.org/docs/Naming-Conventions.html).

### BEM

```scss
.block-name {}
.block-name__child-name {}
.block-name--modifier {}
```

### Montage

```scss
.namespace-BlockName {}
.namespace-BlockName-childName {}
.namespace-BlockName--modifier {}
```

The most important thing is that you pick one as a team and stick with it.

## Selectors

* Use one discrete selector per line in multi-selector rulesets.
* Quote attribute values in selectors, e.g., input[type="checkbox"].
* Place the closing brace of a ruleset in the same column as the first character of the ruleset.
* Separate each ruleset by a blank line.
* Include a single space before the opening brace of a ruleset.

## Properties

* Include one declaration per line in a declaration block.
* Use one level of indentation for each declaration.
* Use lowercase and shorthand hex values, e.g., #aaa.
* Use single or double quotes consistently. Preference is for double quotes, e.g., content: "".
* Where allowed, avoid specifying units for zero-values, e.g., margin: 0.
* Include a semi-colon at the end of the last declaration in a declaration block.
* Include a space after each comma in comma-separated property or function values.

### Ordering

1. `$variable` should **always** appear at the top. 
2. `@extend` should always appear before properties. It's like extending a class in Ruby.
3. `@include` should appear second. This allows the properties to override the mixins.
4. Properties should appear after this, optionally grouped by type or sorted alphabetically.
5. Mixins with content blocks should appear next. `@include someMixin { properties }`
6. Selectors that target itself. `&.modifier`
7. Child selectors appear last.

The basic rule of thumb is at-rules, properties, then blocks.

Here is an example of a well-formed selector:

```scss
.selector-1,
.selector-2,
.selector-3[type="text"]  {
  $bg: blue;
  $fallback: green;

  @extend .clearfix;
  @include border-box;

  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  display: block;
  font-family: helvetica, arial, sans-serif;
  color: #333;
  background: #fff;
  background: linear-gradient(#fff, rgba(0, 0, 0, 0.8));

  @include after {
    position: absolute;
  }

  &.selector--modifier {
    background: red;
  }

  .selector__child {
    display: none;
  }
}
```

## Nesting

* Avoid nesting more than 2 deep. This is a sign of bad CSS as selectors become too specific.
* Rulesets within selectors should be separated by a single line and follow the same rules as any other selector.

## Indentation

* Indentation should be 2 spaces

## File Structure

* Each logical module of code should belong in its own file. Avoiding putting multiple objects in the same file. This allows you to use the filesystem to navigate your Sass rather than relying on comment blocks.
* Mixins/placeholders/functions should, if possible, belong in their own file.
* Files should be named for the component they are housing. A `block-list` object will live in a `block-list.scss` file.

## Functions

* Functions should be prefix with a dash and a namespace: `-rg-columns`
* The namespace can be dropped if it is a private function: `columns`
* Functions should be documented using DocBlock or similar.

## Mixins

* Mixins should only be used when there are dynamic properties, otherwise use `@extend`
* Mixins that output selectors should be capital-case: `@mixin GridBuilder`
* Mixins that output only properties should be camel-case: `@mixin borderBox`
* Mixins should be prefixed if they are part of a public module: `@mixin as-GridBuilder`
* In general, mixins with logic should not be longer than ~50 lines just like any other programming language
* Private mixins that are not used outside of the current file should be prefixed with a dash: `@mixin -gridHelper`
* Avoid using more than 4 parameters. It is a sign that a mixin is too complex. When Sass adds hashes life will be easier.
* Mixins should be documented

```scss
// Loop through each breakpoint and build
// classes for each using the breakpoint mixins
// First breakpoint is no media query — mobile-first.
// 
// @param {List} $breakpoints List of column breakpoints
// @param {Boolean} $spacing Include spacing classes? 
// @param {Boolean} $visibility Include visibilty classes? 
// @param {Boolean} $layout Include layout classes? 
// @api private
@mixin -rg-Breakpoints($breakpoints, $spacing: true, $visibility: true, $layout: true) {
  @each $columns in $breakpoints {
    @if index($breakpoints, $columns) == 1 {
      @include -rg-BreakpointClasses($columns, $spacing, $visibility, $layout);
    }
    @else {
      @include rg-from($columns) {
        @include -rg-BreakpointClasses($columns, $spacing, $visibility, $layout);
      }
    }
  }
}
```

## Modules/Packages

Sharing Sass code is becoming more important. Without the use of a proper module system in Sass 
we need to establish a few rules so that sharing code is consistent and behaviour is predictable.
Sass packages are popping up in Bower and Github but there is no consistency in the way they 
are implemented. 

A few general rules:

* Every module *must* have a namespace
* Private functions and mixins should be prefixed with a dash: `@mixin -rg-gridUnit`
* Importing a module should not render any selectors 
* Mixins/placholders/functions should be able to be imported separately `@import "rg-Grid/mixins"`
* Avoid relying on global variables.
* Use placeholder selectors whenever possible.
* All global variables must be namespaced.

### File Structure

* Third-party, installed modules should always be placed in a `components` directory.
* Local modules should live in a `local` directory adjacent to the `components` directory.
* All images, fonts and other assets should live in an `assets` directory

Example structure: 

```
/module-name
  /assets
    /fonts
    /images
  /components
    /responsive-grid
    /clearfix
    /animation
  /local
    /homepage
  /lib
    /mixins
    /functions
  bower.json
  index.scss
```

### Namespacing

* Every selector, placeholder, mixin and function that is imported should be namespaced
* Namespaces should be short (2-5 characters) and suffixed with a dash: `rg-Grid`

### Module Entry Point

* Each module should have an `index.scss` file as the entry point: `@import "module-name/index"`. 
* The entry point file does not require an underscore in the file name as each module should be able to be compiled and used individually.
* Importing this entry point file should not render anything in the output
* There must be an **entry-point mixin named for that module**. eg. a `rg-Grid` module would have a `rg-Grid` mixin

### Package Management

* The preferred package manager for Sass packages at the moment is Bower. 
* Avoid registering packages in Bower whenever possible and instead rely on the Github shorthand syntax - `user/project` eg. `fonzie/responsive-grid`
* All packages are installed into the `components` directory

### Load Paths

* A load path to the `components` directory is assumed.

### Dependencies

As every module does not output anything just by being imported, packages can safely import other packages
without the fear that a file has already been imported. Because of this, dependencies can safely require
their own dependencies.

For example, a `Grid` package depends on a `Clearfix` package, but so does the `LayoutHelpers` package. Both
of these packages can `@import "clearfix/index"` without fear that there will be two `.clearfix` classes in the output.

It is assumed that the `components` directory is added as a load path, so packages can easily require their dependencies.


## Definitions

#### Object

A single piece of the design, usually fairly small. This could be things like `.message`, `.block`, `.list`, `.post`.
Objects should be independent. Think of them as lego blocks. Objects have "modifiers" and "children".

#### Children

If an "object" is the parent, any sub-parts of that object are considered its children. Children are only ever
controlled by the object it belongs to. A `.message` object may have a title that it styles, `.message__title`,
this would be referred to as a child element. 

#### Module

A single piece of functionality that can be composed of CSS, mixins, functions and assets (such as images or fonts).
A module has a single entry point and a single purpose and role. eg. A grid framework could be a module.

#### Package

When a module is shared with others via a package manager like Bower it will generally be referred to as a package.
This means that the term "module" and "packages" are fairly interchangable.

#### Block

This is another term for the concept of an "object".

#### Element

When referring to "objects" and "blocks", the word "element" is interchangable with the word "children".

#### Modifier

"Objects" may be modified in a way that changes their style in small ways, think of them as themes or alternative
styles. For example, a `.list` object may have a `.list--small` modifier to make the text smaller.

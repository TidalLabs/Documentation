# {icon question-circle} High-level Preface

Ideally, the goal of a style guide is to make our code look like it was written by one person, speeding up reading & writing code in the long run. Realistically, the goal of this guide, and our front-end skeleton, is to make creating client dev sites faster, not to bog the process down. This style guide therefore favors our team's specific needs while balancing clean/DRY code and industry best practices.

Because we're a small and fast-moving team, I've opted to adopt a more relaxed and less stringent approach to our code standards. We won't be using a CSS or Sass linter and we won't be rapping anyone on the knuckles for [[ https://github.com/impressivewebs/HTML9-Responsive-Boilerstrap-js/commit/98c58d0e0f309f23efe4f06ed47f2ea89525d06e | things like this ]]. The goal of this is to provide a guideline to clean, readable, and reusable code, without being semantic for the sake of being semantic.

# {icon pencil} The Guide

## {icon minus-square} HTML Scaffolding

Use the following HTML5 doctype declaration, language attribute, and meta tags. ([[ http://codeguide.co/#html-doctype | 1 ]])

```
<!DOCTYPE html>
<html lang="en-us">
  <head>
    <meta http-equiv="X-UA-Compatible" content="IE=Edge">
    <meta charset="UTF-8">
  </head>
  ...
</html>
```

---

## {icon minus-square} HTML Syntax

- We use tabs, not spaces. (Note: this may change soon)
- Use double quotes instead of single quotes on attributes.
- Don't include a trailing slash in self-closing elements—the [[ http://dev.w3.org/html5/spec-author-view/syntax.html#syntax-start-tag | HTML5 spec ]] says they're optional.
  - e.g., `<img src="foo.gif">`, not `<img src="foo.gif" />`
- Don’t omit optional closing tags (e.g. `</li>` or `</p>`).

Per HTML5 spec, typically there is no need to specify a type when including CSS and JavaScript files as text/css and text/javascript are their respective defaults.

```
<link rel="stylesheet" href="style.css">
<script src="script.js"></script>
```

---

## {icon minus-square} HTML Comments

Avoid writing closing tag comments, like `<!-- /.element -->`. This just adds to page load time. Plus, most editors have indentation guides and open-close tag highlighting. ([[ http://primercss.io/guidelines/#general-formatting | 2 ]])

---

## {icon minus-square} Suggested HTML Attribute Order

While not enforced, this is a suggested order for attributes that can make it easier to read code.

- `class`
- `id`, `name`
- `data-*`
- `src`, `for`, `type`, `href`, `value`
- `title`, `alt`
- `aria-*`, `role`

---

## {icon minus-square} CSS Syntax

- We use tabs, not spaces. (Note: this may change soon)
- Put a space after `:` in property declarations.
- Put a space before `{` in rule declarations.
- Place closing braces of declaration blocks on a new line.
- Avoid specifying units for zero values, e.g., `margin: 0;` instead of `margin: 0px;`.
- Don't prefix property values or color parameters with a leading zero (e.g., `.5` instead of `0.5`).
- Lowercase all hex values for readability and use shorthand hex values where available, e.g., `#fff` instead of `#FFFFFF`.
- Strive to limit use of shorthand declarations to instances where you must explicitly set all the available values. Common overused shorthand properties include `margin`, `padding`, `background`, and `border`. Excessive use of shorthand properties often leads to sloppier code with unnecessary overrides and unintended side effects. ([[ http://codeguide.co/#css-shorthand | 1 ]])

Good:
```
.ComponentName {
  padding: 15px;
  margin-bottom: 15px;
  background-color: rgba(0,0,0,.5);
  box-shadow: 0 1px 2px #ccc, inset 0 1px 0 #fff;
}
```
Bad:
```
.ComponentName
{
  padding:15px;
  margin:0px 0px 15px;
  background-color:rgba(0, 0, 0, 0.5);
  box-shadow:0px 1px 2px #CCC,inset 0 1px 0 #FFFFFF
}
```

---

## {icon minus-square} Suggested CSS Property Order

This isn't definitive, but I would suggest [[ https://github.com/twitter/recess/blob/master/lib/lint/strict-property-order.js#L36 | Twitter's RECESS ]] order, which follows this grouping/hierarchy:

1. Positioning
2. Box model
3. Typographic
4. Visual

The only adjustment I would personally make is to put `z-index` always above everything else - in my opinion it's one of the trickier properties to keep an eye on.

Example:
```
.DeclarationOrder {
  /* Positioning */
  z-index: 100;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;

  /* Box-model */
  display: block;
  float: right;
  width: 100px;
  height: 100px;
  padding: 5px;
  margin: 5px;

  /* Typography */
  font: normal 13px "Helvetica Neue", sans-serif;
  line-height: 1.5;
  color: #333;
  text-align: center;

  /* Visual */
  background-color: #f5f5f5;
  border: 1px solid #e5e5e5;
  border-radius: 3px;

  /* Misc */
  opacity: 1;
}
```

---

## {icon minus-square} Media Query Placement

Place media queries as close to their relevant rule sets whenever possible. Don't bundle them all in a separate stylesheet or at the end of the document. Doing so makes it easier for others to miss them in the future.

NOTE: We are have not done this so far in any of our stylesheets, so this will be fairly significant change for us to adopt.

Example (Sass):

```
.ComponentName {
  width: 25%;

  @include breakpoint(medium) {
    width: 100%;
  }
}
```

---

## {icon minus-square} Suggested CSS Single Declarations
In instances where a rule set includes only one declaration, consider removing line breaks for readability and faster editing.

Example:
```
.ComponentName           { background-position: 0 0; }
.ComponentName-home      { background-position: 0 -20px; }
.ComponentName-account   { background-position: 0 -40px; }
```

---

## {icon minus-square} CSS Specificity, Selectors, & Nesting

### {icon minus-square} Specificity

- To prevent specificity issues, avoid using `ID`s as CSS styling hooks ([[ http://cssguidelin.es/#ids-in-css | why? ]]). However, `ID`'s can and should still be used for HTML and Javascript.
- To keep our JS & CSS clean and separate ([[ http://philipwalton.com/articles/decoupling-html-css-and-javascript/ | why? ]]), avoid hooking them onto the same classes. We suggest a `js-` prefix for Javascript hooks, and keeping `js-` classes out of stylesheets.

### {icon minus-square} Selectors

- **Whenever declaring your styles, use the least number of selectors required to style an element.**
- Favor classes, preferably prefixed classes, over generic tag elements, e.g. `.menu-item` over `ul li`.
- Keep selector bloat to a minimum. Limit the number of elements in each selector to a max of 2. Ideally, our chosen naming convention will prevent this altogether.
- Why's & additional reading:
  - [[ http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/ | Shoot to kill; CSS selector intent ]]
  - [[ http://markdotto.com/2012/03/02/stop-the-cascade/ | Stop the cascade ]]

### {icon minus-square} Nesting

- **Don't nest more than two levels deep,** for the same reasons we limit selectors above.
-  Ideally, our chosen naming convention should prevent over-nesting altogether.

---

## {icon minus-square} CSS Naming Convention

> 9 out of 10 developers agree: naming things is by far the most difficult part of writing CSS. This is because we can't predict the future. A class name might make perfect sense one day, then the design changes, and it's a misnomer the next day.
> -[[ http://seesparkbox.com/foundry/naming_css_stuff_is_really_hard | Naming CSS Stuff Is Really Hard ]]

Our attempt at making this less difficult is to adopt an already-exact naming standard. For our use case, we opted to go with [[ https://github.com/suitcss/suit/blob/master/doc/naming-conventions.md | SUIT CSS]] instead of BEM.

SUIT uses **pascal case** for component names, **camel case** for descendants & modifiers, **one hyphen** for descendants, and **two hyphens** for modifiers.

Example:
```
.u-utility {} 
.ComponentName {} 
.ComponentName--modifierName {} 
.ComponentName-descendantName {} 
.ComponentName.is-someState {} 
```

Note that the `is-someState` can be used in both CSS/Javascript - if the //only// purpose of the Javascript function is to toggle a CSS class, use `is-someState` as a hook for both languages.

Example SCSS:
```
.ComponentName {
  &.is-someState {
    ...
  }

  &--modifierName {
    ...
  }

  &-descendantName {
    ...
  }
}
```

See [[ https://github.com/suitcss/suit/issues/28 | this thread ]] for an example of real code (and problem solving) with SUIT naming.

### {icon minus-square} Name classes for their function/structure/content rather than presentation

This is not a hard and fast rule and at times might feel impossible to follow. To the best of your ability, try to make class names as un-opinionated as possible. For example:

`.Button--primary` rather than `.Button--large` or `.Button--green`

---

## {icon minus-square} Sass Organization

### {icon minus-square} Example Folder Structure
```
css
├── components
│   ├── _post-iterator.scss
│   ├── _main-footer.scss
│   └── _main-header.scss
├── globals
│   ├── _grid.scss
│   ├── _mixins.scss
│   ├── _resets.scss
│   ├── _utilities.scss
│   └── _variables.scss
├── plugins
│   ├── _jquery.featherlight.css
├── sections
│   ├── _post-page.scss
│   └── _user-page.scss
├── _shame.css
├── style.css
└── style.scss
```

### {icon minus-square} Example `style.css`

- `style.css` is just a table of contents.
- Break into as many small files as there are components.
- Partials are named `_partial.scss` (note underscore).

```
/* Plugin Dependencies */
@import "plugins/jquery.featherlight";

/* Authored Dependencies */
@import "globals/variables";
@import "globals/mixins";
@import "globals/resets";
@import "globals/utilities";
@import "globals/grid";

/* Components & Reusable Patterns */
@import "components/main-header";

/* Sections */
@import "sections/post-page";

/* Shame - a section for quick fixes to be cleaned up later */
@import "shame";
```

### {icon minus-square} Suggested Sass Property Order

1. Extends
2. Includes
3. Regular CSS
3. Pseudo-selectors
4. Nested Selectors
5. Descendants

Example:
```
.ComponentName {
  @extend %module;
  @include transition(all .4s);

  background: red;

  &:hover {
    ...
  }

  &:before {
    ...
  }

  &.is-someState {
    ...
  }

  &--modifier {
    ...
  }

  &-descendant {
    ...
  }
}
```

### {icon minus-square} Compiling

- Compile in `--style compressed` mode always, to minify our CSS.
- Compile with source maps, but **be sure** to add `style.css.map` to the `.gitignore` file so we don't commit it to prod.

([[ https://css-tricks.com/sass-style-guide/ | 3 ]])

---

## {icon minus-square} CSS Architectural Principles

These are principles to keep in mind while writing code. Like most of this guide, we're not advocating strict adherence, but rather suggesting to use these principles as the underlying motivation and guideline for writing new code.

- [[ http://cssguidelin.es/#dry | DRY ]]
  - Don't Repeat Repeat Yourself - "The key isn’t to avoid all repetition, but to normalise and abstract meaningful repetition. If two things happen to share the same declarations coincidentally, then we needn’t DRY anything out."
- [[ http://cssguidelin.es/#object-orientation | Object-Oriented CSS ]]
  - "OOCSS deals with the separation of UIs into structure and skin: breaking UI components into their underlying structural forms, and layering their cosmetic forms on separately. ... OOCSS promotes reuse of code, which makes us quicker, as well as keeping the size of our codebase down."
- [[ http://cssguidelin.es/#the-single-responsibility-principle | The Single Responsibility Principle ]]
  - "Keeping your layout and theme selectors separate and structural classes free of presentational properties (such as borders, background, colours and padding) helps ensure that those classes can be re-used throughout a project."
- [[ http://www.zell-weekeat.com/how-to-write-mobile-first-css/ | Mobile-first CSS ]]
  - The goal of mobile-first CSS to reduce loading times and data transfer for mobile visitors. "[S]tyles are applied first to mobile devices. Advanced styles and other overrides for larger screens are then added into the stylesheet via media queries."

# {icon quote-left} Sources

1. [[ http://codeguide.co/ | Code Guide by @mdo ]]
2. [[ http://primercss.io/guidelines/ |  Primer by Github ]]
3. [[ https://css-tricks.com/sass-style-guide/ | Sass Style Guide by Chris Coyier ]]
4. [[ https://github.com/suitcss/suit/blob/master/doc/naming-conventions.md | SUIT CSS naming convention ]]

# {icon link} External Resources and Food for Thought

-  **[[ http://sixrevisions.com/css/css-methodologies/ | A Look at Some CSS Methodologies ]] / [[ https://medium.com/@cathy_dutton/css-layers-afff4ae66649 | CSS Layers ]]** - Short primers to various CSS naming conventions and methodologies, including BEM, SUITCSS, OOCSS, SMACSS, and ITCSS

- **[[ http://cssguidelin.es/ | CSS Guidelines ]]** by Harry Roborts - A more strict and opinionated CSS guideline than would be useful for our team, but is nevertheless full of good information and base concepts worth reading.

- **[[ http://philipwalton.com/articles/css-architecture/ | CSS Architecture ]]** and **[[ http://philipwalton.com/articles/side-effects-in-css/ | Side Effects in CSS ]]** by Philip Walton - Also fairly opinionated and rigid, but has some very good food for thought behind it.

- **OOCSS (multiple) vs `@extend` (one class)**
  - **[[ https://medium.com/@cathy_dutton/surviving-css-be306ef3de6d | Surviving CSS ]]** - Advocates using Sass's `@extend` function over multiple classes & OOCSS.
  - **[[ http://speckyboy.com/2015/06/17/writing-good-css/ | Writing Good CSS]]** - Advocates for OOCSS/BEM.
  - **[[ http://www.sitepoint.com/atomic-oobemitscss/ | Atomic OOBEMITSCSS]]** - Oddly enough, advocates for both

- **[[ http://benfrain.com/enduring-css-writing-style-sheets-rapidly-changing-long-lived-projects/ | Enduring CSS: writing style sheets for rapidly changing, long-lived projects ]]** by Ben Frain - A lengthy & realistic approach to developing CSS in fast-paced environments.

- **[[ http://madewithenvy.com/ecosystem/articles/2015/css-definitive-answers/ | In CSS, the Only Wrong Answers are Definitive Ones ]]** - A reassurance that there's no universal right standard, just the one that works for your team. The best standard is the one that you pick and consistently stick to.

- **[[ https://css-tricks.com/what-a-css-code-review-might-look-like | What a CSS Code Review Might Look Like ]]** - Great and easy to understand overview of what to look for when reviewing CSS.

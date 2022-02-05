---
title: 精读《Airbnb CSS / Sass Styleguide》
date: 2022-02-05 14:57:34
tags:
  - css
  - sass
  - style guide
---

# 精读《Airbnb CSS / Sass Styleguide》

## Formatting

- Prefer dashes over camelCasing in class names.

```
<!-- bad -->
.avatar{
    border-radius:50%;
    border:2px solid white; }
.no, .nope, .not_good {
    // ...
}
#lol-no {
  // ...
}

<!-- good -->
.avatar {
  border-radius: 50%;
  border: 2px solid white;
}

.one,
.selector,
.per-line {
  // ...
}
```

## BEM

**BEM**, or “Block-Element-Modifier”, is a naming convention for classes in HTML and CSS. It was originally developed by Yandex with large codebases and scalability in mind, and can serve as a solid set of guidelines for implementing OOCSS.

## Border

Use 0 instead of none to specify that a style has no border.

## SASS

### Ordering of property declarations

#### 1. Property declarations

List all standard property declarations, anything that isn't an @include or a nested selector.

```scss
.btn-green {
  background: green;
  font-weight: bold;
  // ...
}
```

#### 2. `@include` declarations

Grouping @includes at the end makes it easier to read the entire selector.

```scss
.btn-green {
  background: green;
  font-weight: bold;
  @include transition(background 0.5s ease);
  // ...
}
```

#### 3. Nested selectors

Nested selectors, if necessary, go last, and nothing goes after them. Add whitespace between your rule declarations and nested selectors, as well as between adjacent nested selectors. Apply the same guidelines as above to your nested selectors.

```scss
.btn {
  background: green;
  font-weight: bold;
  @include transition(background 0.5s ease);

  .icon {
    margin-right: 10px;
  }
}
```

### Variables

Prefer dash-cased variable names (e.g. $my-variable) over camelCased or snake_cased variable names. It is acceptable to prefix variable names that are intended to be used only within the same file with an underscore (e.g. $\_my-variable).

### Mixins

Mixins should be used to DRY up your code, add clarity, or abstract complexity--in much the same way as well-named functions. Mixins that accept no arguments can be useful for this, but note that if you are not compressing your payload (e.g. gzip), this may contribute to unnecessary code duplication in the resulting styles.

### Extend directive

@extend should be avoided because it has unintuitive and potentially dangerous behavior, especially when used with nested selectors. Even extending top-level placeholder selectors can cause problems if the order of selectors ends up changing later (e.g. if they are in other files and the order the files are loaded shifts). Gzipping should handle most of the savings you would have gained by using @extend, and you can DRY up your stylesheets nicely with mixins.

### Nested selectors

Do not nest selectors more than three levels deep!

```scss
.page-container {
  .content {
    .profile {
      // STOP!
    }
  }
}
```

When selectors become this long, you're likely writing CSS that is:

- Strongly coupled to the HTML (fragile) —OR—
- Overly specific (powerful) —OR—
- Not reusable

**Again: never nest ID selectors!**

If you must use an ID selector in the first place (and you should really try not to), they should never be nested. If you find yourself doing this, you need to revisit your markup, or figure out why such strong specificity is needed. If you are writing well formed HTML and CSS, you should never need to do this.

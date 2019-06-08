<p align="center">
  <img width="460" height="300" src="bumcss-logo.svg">
</p>

# bumcss (wip)
*Best Utility Modularization CSS -  for the lazy and pragmatic* 

bumcss is a (S)CSS methodology of writing (mostly) semantic, scalable and maintainable CSS that aims to avoid classitis, or in other words at a most expressive html, with least amount of code to write. The approach is specifically useful when developing Single Page Applications using JavaScript frameworks such as Vue.js, React or Angular or web components, where getting a quick grasp on what's happening is most important for development, but it can be also used for classic web pages.

bumcss It's inspired by BEM and SMACSS, which in the old days, when spaghetti CSS was a legitimate strategy, were quite the big improvement. Both approaches require you to assign a lot of classes. Too much code in my humble opinion which potentially not only slows down loading times, but more importantly increases development time and makes the code less readable, because there is simply so much more of it.

### Very basic example
```html
<button class="btn blue">
  Hello
</button>
```
```scss
// btn.scss
.btn {
    display: inline-block;
    border: 1px solid gray;
    padding: 5px;

    &:hover {
      border-color: black;
    }

    // variant styles
    &.blue {
      background: blue;
      color: #fff;
      
      &:hover {
        background: lightblue;
        color: black;
      }
    }
}
```

## UI Components, Wrapper Components and Default Styles
A typical application has three types of elements.

**Global Default Styles** for elements such as tables, links, headings, paragraphs, strong, etc. They should be imported first so they can be easily overwritten if needed.

**Wrapper Components/Logical Components** such as pages, or logical sections, let's say a product info component or a shopping cart. Always block elements in the spirit of BEM.

**UI Components** such as a button, tabs or an accordion and so on. They only should have presentational logic and can be block elements, as well as inline elements.

### In the spirit of block, element, modifier
Almost all of your ui components should be responsive. You should be able to place them in different sizes of responsive and non-responsive containers, without breaking their styles. 

## Order of styles: Simple UI Component Example
The fundamental principle of bumcss is that all styles belonging to element should be grouped as closely together as possible. 

Some Code says more than 100 lines of theory. So here is some practical example: 
```html
<button class="btn blue-variant">
  <span>hello</span>
  <icon>fish</icon>
</button>
```

```scss
.btn {
    /* Different states & variants of the element itself */
    // NOTE: variant names should always be adjectives while element names (e.g. btn) should never be
    &.blue-variant {}
    // camel cased and usually prefixed with "is" or "has" to distinguish them visually
    &.isVisible {}
    // pseudo states
    &:hover {}
    
    // parent modifiers, used for themes or for parent states & variants 
    .global-theme-class & {}
    .parent.variant & {}
    .parent.isExpanded & {}

    // pseudo selectors    
    &:first-child {}

    // sibling selectors (probably rarely used)
    ~ .btn {}


    /* Sub element selectors
    While these should normally be avoided, there are two exceptions:
    1. Child elements and pseudo elements that usually don't have any styling on them.
    2. Positioning of global ui elements of global ui components and default elements. 
    This is fine, because they normally should not have any positioning in the 
    first place or can be easily overwritten in the case of default styles.*/

    // pseudo elements
    &:after {}
  
    // normally unstyled sub elements
    span {
      font-size: 20px;
    }
    &.variant span {}
    &.isVisible span {}
  
    // sub ui elements which are only positioned or shown/hidden, 
    // e.g. margin, position, left, top, right, bottom, display, visibility, flex
    icon {
        position: absolute;
        right: 10px;
    }
    &.blue-variant .icon {}
    &.isVisible .icon {}
}
```

## CSS Specificity: Complex Component Example
For more info on CSS specifity have a look [here](http://cssspecificity.com/) and [here](https://specificity.keegan.st/).
```html
<tabs>
  <tab class="big">
    <tab-heading>heading <icon>fish</icon></tab-heading>
    <tab-content></tab-content>
  </tab>
  <tab class="blue">
    <tab-heading class="funny">heading <icon>icon</icon></tab-heading>
    <tab-content class="boxed"></tab-content>
  </tab>
</tabs>
```
```scss
// components/tabs/tabs.scss
// or for global scss: scss/components/tabs.scss

// el S001
tabs {
  // ...
}

// el S001
tab {
  // states S011 
  &.isActive {
  }
  
  // variant S011
  &.blue {
    // variant + state S021
    &.isActive{}
  }

  // variant S011
  &.big {
    // variant + state S021
    &.isActive{

      // DON'T DO THIS, if you don't have to, don't nest elements
      tab-heading {}
    }
  }
}

// el S001
tab-heading {
  // state (pseudo selector) S011
  &:hover{
    color: blue;
  }
  // parent state S012
  tab.isActive & {
    color: red;
    
    // parent state + state S021
    &:hover {
      color: darkred;
    }
  }
  
  // parent variant S012
  tab.big & {
    font-size: 20px;
    
    // every sub element and variant gets its own media query
    @media (max-width: $screen-xs) {}
  }
  
  // child el S 002
  icon {
    margin: 5px;
    
    // parent variant S013
    @at-root tab-heading.funny &{
      transform: scale(2) rotate(13deg);
    }
    
    // parent parent state S013
    tab.isActive & {
      transform: rotate(360deg);
    }
  }
}

// el S001
tab-content {
  // parent state S012
  tab.isActive & {}
  
  // ...
}
```

### Specificity
You can see that specificity and conflicting styles are not a problem most of the time if you stick to the described pattern. The only exceptions are the "parent variant" & the "parent parent variants" and "variants" vs "states". It's up to you, what you think, should have prevalence. 

#### Variants vs States
Personally I think it's nicer to write states first, because they are closer related the the default main element while variants could be considered a new variant of the same element. On the other hand I think that states also should have prevalence over variants. Most of the time this shouldn't be a problem, as states are more likely to focus on transforms and visibility, or in other words, what a component does on the page, while variants are more likely to focus on properties that change the general appearance such as font-sizes, colors, borders and box-shadows.

Having them so closely written together, should help you quickly identifying possible conflicts and if you think that a state should have prevalence over a variant in any case, it's probably ok to use `!important` for something like `el.isHidden {display: none !important;}`, as you are likely to want to apply `display: none` a 100% of the time.

#### Parent Parent States/Variants vs. Parent States/States Variants
Probably not a problem most of the time, as it's unlikely too have many combinations of those. But as with with "Variants vs. States" writing them closely together, should help you to quickly identify problematic areas and order them accordingly.

## Wrapper/Logical Component example
```html
<div class="product">
    <header class="product-header">
      <h1>Product Header</h1>
    </header>
    
    <section class="product-info">
      <h2 class="fancy">Product Info</h2>
      <tabs>
        <tab class="big">
          <tab-heading>heading <icon>fish</icon></tab-heading>
          <tab-content></tab-content>
        </tab>
        <tab class="blue">
          <tab-heading class="funny">heading <icon>icon</icon></tab-heading>
          <tab-content class="boxed"></tab-content>
        </tab>
      </tabs>
    </section>
</div>

```
```scss
// global default styles in their own file(s)
// should be included first so they can be overwritten
h2 {
  // S 011
  .fancy {
    // ...
  }
  
  // DON'T DO THIS 
  // Global default styles should be applied regardless of context
  header &.fancy{
  }
}
```

```scss
.product-info {

  // remember adjusting positioning is ok, everything else should be a variant
  // S 011 => overwrites default styles, even for variants
  h2 {
    margin-top: 0;
    margin-bottom: 20px;
  }
  
  tabs {
    margin-top: 30px;    
  }
  // DON'T DO THIS
  // this probably better done inside the file for the tabs as a variant
  // e.g. tab-heading.blue {}
  tab-heading {
    color: blue;
  }
}

```

## File and Folder Structure
This depends strongly on if and which JavaScript framework you use. But general rules of thumb are:
* create a global scss folder, for things like default styling of:
    * headings
    * text (e.g. p, em, i, b, strong, etc.)
    * tables
    * inputs
    * buttons
    * links
* you normally shouldn't have to overwrite those global styles too often. If you do that's a strong sign that things are messily designed in the first place. So go and talk to your designer! ;)
* grouping files by component is preferable to grouping files by type
* global default styles should be imported first, so they can be overwritten

## General Tips and Tricks
* having a consistent system for vertical margins helps. Use scss variables or mixins.
* use variables or mixins for different font styles, but prefer using the default tags e.g. h1, h2, p, strong, table, etcs. where possible
* having a good system for the base styles will save you a lot of code

## Grids
Grid utility classes are handy in my opinion while you give a quick indication on what's going on. Not semantic maybe but practical, so it's totally fine to use them.

## A word on utility classes
Personally not the biggest fan, but if you feel, it's very useful for your particular case, let's say for vertical margins, then use them. But if you do so, be consistent. Mixing multiple approaches can lead to chaos.  

## Global Box Sizing
Yes, please!

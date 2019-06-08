# THIS IS A DRAFT AND WORK IN PROGRESS

# bumcss (best utility modularization)
bumcss is a (S)CSS methodology of writing semantic, scalable and maintainable CSS that aims to avoid classitis, or in other words at a most expressive html, with least amount of code to write.   

Not being satisfied with existing methodologies of writing clean CSS, I though it is time to write my own. It's inspired by BEM and SMACSS, which in the old days, when spaghetti CSS was a legitimate strategy, were quite the big improvement. But imho it's too way too verbose. Too much code which potentially slows down loading times, but more importantly increases development time and making code less readable, because there is so much of it.

## Simple UI Component
```html
<button class="btn blue-variant">
  <span>hello</span>
  <icon>fish</icon>
</button>
```

```scss
// one of those
:host,
.btn {
    /* Different states & variants of the element itself */
    // NOTE: variant names should always be adjectives while element names should never be
    &.blue-variant {}
    // NOTE: states are the only time !important CAN be acceptable though it should be avoided
    // camel cased and usually prefixed with "is" or "has" to distinguish them visually
    &.isVisible {}
    
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
    1. Child elements, that usually don't have any styling on them.
    2. Positioning of global ui elements of global ui elements (e.g. icons). 
    This is fine, because they normally should not have any positioning in the 
    first place.*/

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

## Complex Component Example With CSS Specificity
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

      // DON'T DO THIS
      // if you don't have to, don't nest elements
      tab-heading {}
    }
  }
}

// el S001
tab-heading {
  // parent state S012
  tab.isActive & {
    color: red;
  }
  
  // parent variant S012
  tab.big & {
    font-size: 20px;    
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

## Non Ui Component example
```html
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
## In the spirit of block, element, modifier
...

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

## Grids
Grid utility classes are handy in my opinion while you give a quick indication on what's going on. Not semantic maybe but practical, so it's totally fine to use them.

## A word on utility classes
Personally not the biggest fan, but ff you feel, it's very useful for your particular case, let's say for vertical margins, then use them. But if you do so, be consistent. Mixing multiple approaches can lead to chaos.  

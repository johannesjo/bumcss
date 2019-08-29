<h1 align="center">bumcss (wip)</h1>
<p align="center"<i>Best Utility Modularization CSS -  for the lazy and pragmatic</i></p>

<p align="center">
  <img width="460" height="300" src="bumcss-logo-with-text.svg">
</p>

bumcss is a [(S)CSS](https://sass-lang.com/) methodology of writing (mostly) [semantic](https://alistapart.com/article/meaningful-css-style-like-you-mean-it/), scalable and maintainable CSS that aims at *the most expressive semantic html*, with least amount of code to write. The approach is specifically useful when developing Single Page Applications using JavaScript frameworks such as Vue.js, React or Angular or web components, where getting a quick grasp on what's happening is most important for development, but it can be also used for classic web pages.

bumcss It's inspired by [BEM](http://getbem.com) and [SMACSS](http://smacss.com). The problem with both approaches is that they require you to assign a lot of classes (aka [classitis](https://www.steveworkman.com/html5-2/standards/2009/classitis-the-new-css-disease/)). Too much code in my humble opinion which potentially not only slows down loading times, but more importantly increases development time and makes the code less readable, because there is simply so much more of it.

## Why conventions are so important
A lot has been [written](https://www.multidots.com/importance-of-code-quality-and-coding-standard-in-software-development/) [on](https://medium.com/@marksiu/why-naming-convention-is-a-must-for-your-development-team-628188f392d5) [that](https://www.google.com/search?q=why+conventions+are+important+in+software+development&oq=why+conventions+are+important+in+software+development&aqs=chrome..69i57.11723j0j7&sourceid=chrome&ie=UTF-8). They are! And (S)CSS has been traditionally the place where there are neglected the most.

## Very basic example
```html
<button class="btn _emphasized">
  Hello
</button>
```
```scss
// btn.scss
.btn {
    display: inline-block;
    border: 1px solid gray;
    padding: 5px;
    
    @media(min-width: 780px) {
      padding: 10px;
    }

    &:hover {
      border-color: black;
    }

    // variant styles
    &._emphasized {
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
Let's start with the very basics. A typical application will - for the most past - have three types of elements.

**Global Default Styles** for basic elements such as tables, links, headings, paragraphs, strong, etc. They should be imported first so they can be easily overwritten if needed. Having well designed foundation of base styles and limiting yourself to fixed set of e.g. heading styles, will save you a lot of work in the long run.

**Wrapper Components/Logical Components** such as pages, or logical sections, let's say a or a shopping cart or a map component. Always block elements in the spirit of BEM. In a single page application those components will likely be [the place to distribute data throughout your UI Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0).

**UI Components** such as a button, tabs or an accordion and so on. They only should have presentational logic and can be block elements, as well as inline elements.

### In the spirit of block, element, modifier
Almost all of your ui components should be responsive. You should be able to place them in different sizes of responsive and non-responsive containers, without breaking their styles. 

## Order of styles: Simple UI Component Example
The fundamental principle of bumcss is that all styles belonging to element should be grouped as closely together as possible. 

Some Code says more than 100 lines of theory. So here is some practical example: 
```html
<button class="btn _blue-variant">
  <span>hello</span>
  <icon>fish</icon>
</button>
```

```scss
.btn {
    /* Different states & variants of the element itself */
    // NOTE: variant names should either always be adjectives (while element names, e.g. btn, should never be) or they should have a prefix like "_"
    &._blue-variant {}
    // camel cased and usually prefixed with "is" or "has" to distinguish them visually
    &.isVisible {}
    // pseudo states
    &:hover {}
    
    // parent modifiers, used for themes or for parent states & variants 
    .global-theme-class & {}
    .parent._variant & {}
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
    &._variant span {}
    &.isVisible span {}
  
    // sub ui elements which are only positioned or shown/hidden, 
    // e.g. margin, position, left, top, right, bottom, display, visibility, flex
    icon {
        position: absolute;
        right: 10px;
    }
    &._blue-variant .icon {}
    &.isVisible .icon {}
}
```

## CSS Specificity: Complex Component Example
One of the main arguments for BEM is, that by using only a single class everywhere you won't run into problems where you need to go deeper and deeper to overwrite a style property (For more info on CSS specificity have a look [here](http://cssspecificity.com/) and [here](https://specificity.keegan.st/)). Let's explore how specificity will look like in a bumcss component.


```html
<tabs>
  <tab class="_big">
    <tab-heading>heading <icon>fish</icon></tab-heading>
    <tab-content></tab-content>
  </tab>
  <tab class="_blue">
    <tab-heading class="_funny">heading <icon>icon</icon></tab-heading>
    <tab-content class="_boxed"></tab-content>
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
  &._blue {
    // variant + state S021
    &.isActive{}
  }

  // variant S011
  &._big {
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
  tab._big & {
    font-size: 20px;
    
    // every sub element and variant gets its own media query
    @media (max-width: $screen-xs) {}
  }
  
  // child el S 002
  icon {
    margin: 5px;
    
    // parent variant S013
    // a bit ugly, but probably not needed very often
    @at-root tab-heading.funny & {
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

As you can see that specificity and conflicting styles are not a problem most of the time if you stick to the described pattern. The only exceptions are the "parent variant" & the "parent parent variants" and "variants" vs "states". It's up to you, what you think, should have prevalence. 

#### Variants vs States
Personally I think it's nicer to write states first, because they are closer related the the default main element while variants could be considered more of a new instance of the same element. On the other hand I think that states normally should have prevalence over variants. Most of the time this shouldn't be a problem, as states are more likely to focus on transforms and visibility, or in other words, what a component does on the page, while variants are more likely to focus on properties that change the general appearance such as font-sizes, colors, borders and box-shadows.

Having them so closely written together, should help you quickly identifying possible conflicts and if you think that a state should have prevalence over a variant in any case, it's probably ok to use `!important` for something like `el.isHidden {display: none !important;}`, as you are likely to want to apply `display: none` a 100% of the time (but only do this if you're sure it is really a 100%).

#### Parent Parent States/Variants vs. Parent States/States Variants
Probably not a problems most of the time, as it's unlikely too have many combinations of those. But as with with "Variants vs. States" writing them closely together, should help you to quickly identify problematic areas and order them accordingly if needed.

## Wrapper/Logical Component example
```html
<div class="product">
    <header>
      <h1>Product Header</h1>
    </header>
    
    <section class="product-info">
      <h2 class="_fancy">Product Info</h2>
      <tabs>
        <tab class="_big">
          <tab-heading>heading <icon>fish</icon></tab-heading>
          <tab-content>
            <h2>Description</h2>
            <div class="product-description">
              <p>...</p>
              <p>...</p>
            </div>
            <button>Buy</button>
          </tab-content>
        </tab>
        <tab class="_blue">
          <tab-heading class="_funny">heading <icon>icon</icon></tab-heading>
          <tab-content class="_boxed">
            <h2>Features</h2>
            <ul class="features">
              <li>...</li>
            </ul>
          </tab-content>
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
  ._fancy {
    // ...
  }
  
  // DON'T DO THIS 
  // Global default styles should be applied regardless of context
  header &._fancy{
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
    * (optional) a folder where you put the css files of libraries or overwrites of those styles
* you normally shouldn't have to overwrite those global styles too often. If you do that's a strong sign that things are messily designed in the first place. So go and talk to your designer! ;)
* grouping files by component is preferable to grouping files by type
* global default styles should be imported first, so they can be overwritten

## General Tips and Tricks
* Having a consistent system for vertical margins helps. Use scss variables or mixins.
* Use variables or mixins for different font styles, but prefer using the default tags e.g. h1, h2, p, strong, table, etcs. where possible
* Having a good system for the base styles will save you a lot of code
* Get in the habit of extracting ui components when you recognize a pattern very often, but also don't overdo it. If you find yourself with a button component with 50 different variants, chances are that something went wrong.
* Talk to your designer if you recognize inconsistencies you don't understand. Chances are they are not there on purpose.

### Grids
Grid utility classes are handy in my opinion while you give a quick indication on what's going on. Not semantic maybe but practical, so it's totally fine to use them.

### A word on utility classes
Personally not the biggest fan and I would recommend a healthy bit of scepticism, but if you feel, it's very useful for your particular case, let's say for vertical margins or different font styles and sizes then use them. But if you do so, be consistent. Mixing multiple approaches can lead to chaos.  

### Global Box Sizing
Yes, please!

<!--
## Adjustments to approach when all css classes are global
Use BEM sub element classes like `.product__info, .product__description, .product__features`
-->

# THIS IS A DRAFT AND WORK IN PROGRESS

# bumcss (best utility modularization)
bumcss is a (S)CSS methodology of writing semantic, scalable and maintainable CSS that aims to avoid classitis.

Not being satisfied with existing methodologies of writing clean CSS, I though it is time to write my own. It's inspired by BEM, which in the old days, when spaghetti CSS was a legitimate strategy, was quite the big improvement. But it's too way too verbose. Too much code which potentially slows down loading times, but more importantly increases development time and making code less readable, because there is so much of it.

## Example
```html
<button class="btn blue-variant">
  <span>hello</span>
  <icon>fish</icon>
</button>
```

```scss
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

    // sibling selectors (probably not needed in most cases)
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
  
    // sub ui elements which are only positioned
    icon {
        position: absolute;
        right: 10px;
    }
    &.blue-variant .icon {}
    &.isVisible .icon {}
}
```

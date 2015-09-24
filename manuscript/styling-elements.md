# Styling Elements (in progress) {#styling-elements}

Writing current in progress. Check back soon!


## There are three ways to style elements

### Inlining styles using markup

%% ----TALKING ABOUT THE ATTR-----
%% While the `class` attribute is often used for styling elements, it is also used for selecting and classifying them. However, the `style` attribute is used _exclusively_ for adjusting the appearance of an element. This attribute was first introduced in 1996 [in the first formal CSS specification][css1-style].
%% -----TODO: DISCUSS THE ATTR MORE....------


### Working with styles directly on the `Element` object

%% ------TALKING ABOUT THE PROPERTY-----
%% A property on the object representation of an element was first introduced in the year 2000 as [part of DOM Level 2][dom2-style]. This `style` property was defined as the lone property of a new `ElementCSSInlineStyle` interface. For the most part, the `Element` interface implements `ElementCSSInlineStyle`, which allows elements to be styled programmatically using JavaScript. All CSS properties, such as `opacity` and `color` are accessible as properties on the associated [CSSStyleDeclaration][dom2-cssstyledeclaration] instance, where they can be read or updated.
%% -----TODO: DISCUSS THE PROP MORE....------

%% While jQuery relies heavily on the `style` attribute...


### Style by CSS classes



## Changing and reading your element's styles

### The familiar jQuery approach

### Using the web API

#### Using the `style` attribute

#### Using the `Element.style` property

#### Using CSS classes & stylesheets


## Determining width, height, and position of any element



## Reading and updating scroll positions



[css1-style]: http://www.w3.org/TR/REC-CSS1/#containment-in-html

[dom2-cssstyledeclaration]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-CSSStyleDeclaration

[dom2-style]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-ElementCSSInlineStyle

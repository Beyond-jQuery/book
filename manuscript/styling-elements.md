# Styling Elements {#styling-elements}

If you're used to using jQuery's `css` method to work with styles in your document, this chapter is for you. I can certainly relate to blind dependence on this magical aspect of the API. Adjusting the dimensions, color, opacity, and any other style imaginable is _really_ easy to do with the help of jQuery. Unfortunately, this simplicity comes at a substantial cost.

jQuery's internal code that backs its easy-to-use CSS API is horrendous in terms of performance. If you value efficiency at all, if you want to provide your users with an optimal experience, you should learn how to properly manipulate and read element styles using the web API. Instead of relying on a "one size fits all" approach, you should choose the leanest route possible by bypassing jQuery's abstraction.

You may continue to rely on jQuery, or get rid of it entirely in favor of a more "natural" programmatic approach. But there are other concepts to be aware of other than which JavaScript method to use. Consider the possibility that JavaScript is not always the best way to adjust styles in your document. In addition to HTML and JavaScript, the browser provides a third valuable tool - stylesheets.

"Beyond jQuery" aims to provide you with a better understanding of the options provided natively by your browser, with each chapter building on your newfound knowledge. In this chapter, you'll learn some new things about working with element styles both with and without JavaScript. You'll learn enough from this chapter to understand when to target elements using CSS rules in stylesheets instead of resorting to JavaScript 100% of the time. Your strong knowledge of [selectors](#finding-elements) and [attributes](#element-attributes), courtesy of the last few chapters, will make this much easier.


## There are three ways to style elements

Before I dive into examples and details related to actually adjusting and reading back style information from elements in your document, it's important to get a few key concepts out of the way first. In this chapter, I'll show you three distinct routes you may take when working with element styles. The first covers managing styles in your markup - something that is not recommended by still possible. Another method involves making changes to standardized properties on `Element` objects - one approach you may elect to take if you intend to read or update styles on-demand. Finally, I'll go over using CSS inside of stylesheets as a third option.


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

#### The `style` attribute

#### The `Element.style` property

#### CSS classes & stylesheets


## Determining width and height of any element

%% Discuss the box model
%% Different ways to measure width and height: w/ and w/out border

### Examining an element using jQuery


### Options natively provided by the browser

#### Client vs offset dimensions

#### Modern browsers

#### All browsers

#### Scroll width & height


## Reading and updating scroll positions

### jQuery

### The web API

%% scrollIntoView(), scroll() / scrollTo(), scrollTop / scrollLeft, scrollY / scrollX

#### Absolute scrolling

#### Relative scrolling


[css1-style]: http://www.w3.org/TR/REC-CSS1/#containment-in-html

[dom2-cssstyledeclaration]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-CSSStyleDeclaration

[dom2-style]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-ElementCSSInlineStyle

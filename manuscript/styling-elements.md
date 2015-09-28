# Styling Elements {#styling-elements}

If you're used to using jQuery's `css` method to work with styles in your document, this chapter is for you. I can certainly relate to blind dependence on this magical aspect of the API. Adjusting the dimensions, color, opacity, and any other style imaginable is _really_ easy to do with the help of jQuery. Unfortunately, this simplicity comes at a substantial cost.

jQuery's internal code that backs its easy-to-use CSS API is horrendous in terms of performance. If you value efficiency at all, if you want to provide your users with an optimal experience, you should learn how to properly manipulate and read element styles using the web API. Instead of relying on a "one size fits all" approach, you should choose the leanest route possible by bypassing jQuery's abstraction.

You may continue to rely on jQuery, or get rid of it entirely in favor of a more "natural" programmatic approach. But there are other concepts to be aware of other than which JavaScript method to use. Consider the possibility that JavaScript is not always the best way to adjust styles in your document. In addition to HTML and JavaScript, the browser provides a third valuable tool - stylesheets.

"Beyond jQuery" aims to provide you with a better understanding of the options provided natively by your browser, with each chapter building on your newfound knowledge. In this chapter, you'll learn some new things about working with element styles both with and without JavaScript. You'll learn enough from this chapter to understand when to target elements using CSS rules in stylesheets instead of resorting to JavaScript 100% of the time. Your strong knowledge of [selectors](#finding-elements) and [attributes](#element-attributes), courtesy of the last few chapters, will make this much easier.


## There are three ways to style elements

Before I dive into examples and details related to actually adjusting and reading back style information from elements in your document, it's important to get a few key concepts out of the way first. In this chapter, I'll show you three distinct routes you may take when working with element styles. The first covers managing styles in your markup - something that is not recommended by still possible. Another method involves making changes to standardized properties on `Element` objects - one approach you may elect to take if you intend to read or update styles on-demand. Finally, I'll go over using CSS inside of stylesheets as a third option.


### Inline styles

A couple chapters back, I introduced you to [the `class` element attribute](#class-attributes). While this attribute is _often_ used for styling elements, it is also used for selecting and classifying them. In this section, I am going to introduce the `style` attribute, which is used _exclusively_ for adjusting the appearance of an element. This attribute is not new; it was first introduced in 1996 [as part of the first formal W3C CSS specification][css1-style].

Let's suppose you have a very simple document with a few heading elements and associated content. You've decided that each `<h2>` should be blue, and each `<h3>` should be green. As a novice web developer, or perhaps a developer without much knowledge of your styling options at all, you may set the color of these headlines using the `style` attribute.

{title="setting styles using the style attribute", lang=html}
~~~~~~~
<h1>Fake News</h1>
<div>Welcome to fakenews.com. All of the news that's unfit to print.</div>

<h2 style="color: blue">World</h2>

<h3 style="color: green">Valdimir Putin takes up knitting</h3>
<div>The infamous leader of Russia appears to be mellowing with age as he reportedly joined a local knitting group in Moscow.</div>


<h2 style="color: blue">Science</h2>

<h3 style="color: green">Sun goes on vacation, moon fills in</h3>
<div>Fed up after over 4 billion years without a day off, the sun headed off to the Andromeda galaxy for a few weeks of rest and relaxation.</div>
~~~~~~~

Looking at the above example, you can see how the headlines have been colored as desired. You can fit multiple styles on a single element, simply by separating the styles with a semicolon. For example, suppose we want to not only color each `<h2>` blue, but also ensure they stand out a bit more by making them bold.

{title="setting multiple styles using the style attribute", lang=html}
~~~~~~~
<h2 style="color: blue; font-weight: bold">World</h2>

...

<h2 style="color: blue; font-weight: bold">Science</h2>
~~~~~~~

_Any_ [standardized style][css2] can be applied to any element, simply by using the `style` attribute as illustrated in the above code fragments. There are other ways to style your elements, s you will learn shortly, so why would anyone choose this particular method? First, specifying your styles alongside your elements directly in the markup seems like an intuitive and reasonable approach. But using the `style` attribute is most likely done out of laziness or naivety, more often than not. It's painfully obvious how easy it is to specify styles for your elements this way.

Styling your document using the `style` attribute, also known as "inline styling", is something you should _almost always_ avoid. Despite its simplicity and intuitiveness, there are a number of reasons why this practice will cause you grief. First, inline styles add quite a bit of noise to your markup. In addition to content, tags, and other attributes, now you have `style` attributes - perhaps for _many_ of your elements. And these attributes could very well contain a number of semicolon-separated styles. As your document begins to grow and become more complex, this is more apparent.

In addition to cluttering up the document, defining styles directly on each element in your markup precludes you from easily re-skinning your page. Suppose a designer took a look at the above code and informed you that the "green" and "blue" color values are a bit too "generic"-looking, and should be substituted for slightly different colors. The designed supplies you with hex codes for the new colors, and this adjustment requires changing `style` attribute for _all_ `<h2>` and `<h3>` elements in your document. This is a common consequence of not following [the "Don't Repeat Yourself" principal][dry] of software development. Overuse of the `style` attribute can result in a maintenance nightmare.

Finally, peppering your page with `style` attributes - or `<style>` elements, which can contain various a set of CSS rules - can result in more overhead. If a single style needs to be changed, now the entire document has to be re-fetched by the browser the next time a user loads the page. If your styles were defined in a more specific location, outside of your markup, style changes could be introduced while still allowing a portion of your page to be fetched from the browser's cache, avoiding an unnecessary round-trip to the server.

I strongly suggest avoiding the use of `style` attributes. There are other much more appropriate options. The initially visible benefits are shadowed by the hardships that will become apparent further down the road.


### Working with styles directly on the `Element` object {#element.style}

%% ------TALKING ABOUT THE PROPERTY-----
%% A property on the object representation of an element was first introduced in the year 2000 as [part of DOM Level 2][dom2-style]. This `style` property was defined as the lone property of a new `ElementCSSInlineStyle` interface. For the most part, the `Element` interface implements `ElementCSSInlineStyle`, which allows elements to be styled programmatically using JavaScript. All CSS properties, such as `opacity` and `color` are accessible as properties on the associated [CSSStyleDeclaration][dom2-cssstyledeclaration] instance, where they can be read or updated.
%% -----TODO: DISCUSS THE PROP MORE....------

%% While jQuery relies heavily on the `style` attribute...


### Stylesheets



## Changing and reading your element's styles

%% Describe a simple styling exercise, implement it using jQuery, and then again using stylesheets and element.style calls where appropriate.


### The familiar jQuery approach

### Using the web API


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

[css2]: http://www.w3.org/TR/CSS2/

[dom2-cssstyledeclaration]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-CSSStyleDeclaration

[dom2-style]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-ElementCSSInlineStyle

[dry]: http://www.artima.com/intv/dry.html

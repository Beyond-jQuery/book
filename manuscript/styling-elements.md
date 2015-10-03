# Styling Elements {#styling-elements}

If you're used to using jQuery's `css` method to work with styles in your document, this chapter is for you. I can certainly relate to blind dependence on this magical aspect of the API. Adjusting the dimensions, color, opacity, and any other style imaginable is _really_ easy to do with the help of jQuery. Unfortunately, this simplicity comes at a substantial cost.

jQuery's internal code that backs its easy-to-use CSS API is horrendous in terms of performance. If you value efficiency at all, if you want to provide your users with an optimal experience, you should learn how to properly manipulate and read element styles using the web API. Instead of relying on a "one size fits all" approach, you should choose the leanest route possible by bypassing jQuery's abstraction.

You may continue to rely on jQuery, or get rid of it entirely in favor of a more "natural" programmatic approach. But there are other concepts to be aware of other than which JavaScript method to use. Consider the possibility that JavaScript is not always the best way to adjust styles in your document. In addition to HTML and JavaScript, the browser provides a third valuable tool - stylesheets.

"Beyond jQuery" aims to provide you with a better understanding of the options provided natively by your browser, with each chapter building on your newfound knowledge. In this chapter, you'll learn some new things about working with element styles both with and without JavaScript. You'll learn enough from this chapter to understand when to target elements using CSS rules in stylesheets instead of resorting to JavaScript 100% of the time. Your strong knowledge of [selectors](#finding-elements) and [attributes](#element-attributes), courtesy of the last few chapters, will make this much easier.


## There are three ways to style elements

Before I dive into examples and details related to actually adjusting and reading back style information from elements in your document, it's important to get a few key concepts out of the way first. In this chapter, I'll show you three distinct routes you may take when working with element styles. The first covers managing styles in your markup - something that is not recommended by still possible. Another method involves making changes to standardized properties on `Element` objects - one approach you may elect to take if you intend to read or update styles on-demand. Finally, I'll go over using CSS inside of stylesheets as a third option.


### Inline styles {#inline-styles}

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

Defining styles in your document via the `style` attribute is _also_ a potential security risk. If you intend to implement a [Content Security Policy][csp-mdn], styling elements using attributes is strictly prohibited in the most basic (and safest) policy definition. A strong Content Security Policy, also known as a CSP, is becoming more commonplace now that all [modern browsers](#modern-browsers) (with the exception of IE9) include support for at least [the initial version of the specification][csp-w3c].

Finally, peppering your page with `style` attributes - or `<style>` elements, which can contain various a set of CSS rules - can result in more overhead. If a single style needs to be changed, now the entire document has to be re-fetched by the browser the next time a user loads the page. If your styles were defined in a more specific location, outside of your markup, style changes could be introduced while still allowing a portion of your page to be fetched from the browser's cache, avoiding an unnecessary round-trip to the server.

I strongly suggest avoiding the use of `style` attributes. There are other much more appropriate options. The initially visible benefits are shadowed by the hardships that will become apparent further down the road.


### Working with styles directly on the `Element` object {#element-style}

The `style` property on the object representation of an element was first introduced in the year 2000 as [part of DOM Level 2][dom2-style]. It was defined as the lone property of a new `ElementCSSInlineStyle` interface. The `Element` interface implements `ElementCSSInlineStyle`, which allows elements to be styled programmatically using JavaScript. And all CSS properties, such as `opacity` and `color` are accessible as properties on the associated [CSSStyleDeclaration][dom2-cssstyledeclaration] instance, where they can be read or updated.

In case all of this talk of style properties isn't clear, let's take another look at the code example from the previous [inline styles](#inline-styles) section. I'll rewrite it by taking advantage of the `style` property that is available on all `Element` objects.

{title="setting styles using the `style` property - all modern browsers + IE8", lang=html}
~~~~~~~
<h1>Fake News</h1>
<div>Welcome to fakenews.com. All of the news that's unfit to print.</div>

<h2>World</h2>

<h3>Valdimir Putin takes up knitting</h3>
<div>The infamous leader of Russia appears to be mellowing with age as he reportedly joined a local knitting group in Moscow.</div>


<h2>Science</h2>

<h3>Sun goes on vacation, moon fills in</h3>
<div>Fed up after over 4 billion years without a day off, the sun headed off to the Andromeda galaxy for a few weeks of rest and relaxation.</div>

<script>
var headings = document.querySelectorAll('h2, h3');

for (var i = 0; i < headings.length; i++) {
  if (headings[i].tagName === 'H2') {
    headings[i].style.color = 'blue';
  }
  else {
    headings[i].style.color = 'green';
  }
}
</script>
~~~~~~~

This seems a bit clumsy, but it illustrates how we can programmatically update styles using the web API. The use of `querySelectorAll` restricts us to IE8 and up, but this really doesn't seem like much of a problem, due to the almost non-existent market share associated with IE7.

In the [inline styles](#inline-styles) section, I built up the initial code fragment to illustrate how to define multiple styles on a single element. Let's see how we can do this with the `style` property.

{title="setting multiple styles using the `style` property - all modern browsers + IE8", lang=html}
~~~~~~~
<h2 style="color: blue; font-weight: bold">World</h2>

...

<h2 style="color: blue; font-weight: bold">Science</h2>

<script>
var headings = document.querySelectorAll('h2');

for (var i = 0; i < headings.length; i++) {
  headings[i].style.color = 'blue';
  headings[i].style.fontWeight = 'bold';
}
</script>
~~~~~~~

Notice that the `font-weight` style name has been converted to camel case, which is perfectly legal, but we can _still_ change this style using the dashed name, if we really want to: `headings[i].style['font-weight'] = 'bold'`.

But we're not done just yet; there is _another_ way to set multiple styles on a single HTML element using the `style` property. The `CSSStyleDeclaration` interface defines a special property - `cssText`. This allows you to read _and_ write multiple styles to the associated element. The value string looks exactly like a collection of semicolon-separated CSS rules, as you can see below.

{title="setting multiple styles using the `style.cssText` property - all modern browsers + IE8", lang=html}
~~~~~~~
<h2 style="color: blue; font-weight: bold">World</h2>

...

<h2 style="color: blue; font-weight: bold">Science</h2>

<script>
var headings = document.querySelectorAll('h2');

for (var i = 0; i < headings.length; i++) {
  headings[i].style.cssText = 'color: blue; font-weight: bold';
}
</script>
~~~~~~~

Why might you want to make use of the `style` property on an element (or elements)? Maybe you are writing a JavaScript library that need to make a few quick adjustments to some elements based on environmental or user input. It may be inconvenient to create and depend on a library-specific stylesheet for these styles as well. Also, styles set using this method will _usually_ override any other styles previously set on the element, which may be your intent.

But be careful about overusing this power. Styles set in this manner are difficult to override via stylesheet rules. This _may_ be your intent, but it also may not. If it is not, and you _want_ to allow stylesheets to easily make adjustments to styles, you will likely want to avoid changing styles using the `style` property. Finally, using the `style` property can make it very difficult to track down style changes, and can clutter up your JavaScript. It seems unnatural for your code to be focused on setting specific element styles, unless this is a rare practice. As you'll see in the next section, this job is better suited for stylesheets.


### Stylesheets

JavaScript isn't the only way to attack styling challenges in the browser. It probably isn't even the _best_ way to deal change the appearance of your elements. The browser provides a dedicated mechanism for styling your document - stylesheets. Stylesheets, which you may use to define all CSS styles for your web document, may be defined in dedicated files, encapsulated in a specific HTML element, or even added to the document via JavaScript on-demand. I'll demonstrate each of these three methods for working with styles in this section.

The `<style>` element, first defined in the [W3C CSS 1 specification][css1], allows us to group all of our styles for an entire document in one convenience location. Take a look at the previous code fragment, this time with style added courtesy of the `HTMLStyleElement`.

{title="setting styles using the `<style>` element - all browsers", lang=html}
~~~~~~~
<style>
h2 { color: blue; }
h3 { color: green; }
</style>

<h1>Fake News</h1>
<div>Welcome to fakenews.com. All of the news that's unfit to print.</div>

<h2>World</h2>

<h3>Valdimir Putin takes up knitting</h3>
<div>The infamous leader of Russia appears to be mellowing with age as he reportedly joined a local knitting group in Moscow.</div>


<h2>Science</h2>

<h3>Sun goes on vacation, moon fills in</h3>
<div>Fed up after over 4 billion years without a day off, the sun headed off to the Andromeda galaxy for a few weeks of rest and relaxation.</div>
~~~~~~~

As you can see, all of the JavaScript code used to style these elements in [the previous section](#element-style) is completely replaced by two lines of CSS. Not only is this a more efficient solution, but it's also much more elegant and straightforward. And if we want to add additional styles, we can easily do so by including them among the existing styles, semicolon-separated.

{title="setting multiple styles using the `<style>` element - all browsers", lang=html}
~~~~~~~
<style>
h2 {
  color: blue;
  font-weight: bold;
}
h3 {
  color: green;
  font-weight: bold;
}
</style>
...
~~~~~~~

The above styles could even be improved a bit, using the power of the multiple selector, which [you learned about earlier](#multiple-selectors).

{title="setting styles using the `<style>` element w/ the multiple selector - modern browsers + IE8", lang=html}
~~~~~~~
<style>
h2, h3 { font-weight: bold; }
h2 { color: blue; }
h3 { color: green; }
</style>
...
~~~~~~~

Jamming your styles into a `<style>` element may be fine for a small set of rules, but this is probably not ideal for a non-trivial document. Perhaps you even want styles to be shared across documents/pages. Duplicating these styles in each HTML document doesn't seem like a reasonable approach. Luckily, there is a better way - stylesheets.


{title="styles.css - external stylesheet - all browsers", lang=html}
~~~~~~~
h2 { color: blue; }
h3 { color: green; }
~~~~~~~

{title="index.html - setting styles using an external CSS stylesheet file - all browsers", lang=html}
~~~~~~~
<link href="styles.css" rel="stylesheet">

<h1>Fake News</h1>
<div>Welcome to fakenews.com. All of the news that's unfit to print.</div>

<h2>World</h2>
...
~~~~~~~

We've defined two files above: styles.css and index.html. The first houses our stylesheet, the second our markup. In our index file, we can pull in all of these styles simply by referencing the styles.css file via the `<link>` element, which [can be seen as early as the HTML 2.0 specification][html2-link]. This may not be new knowledge for many of you, but it's easy to lose sight of the entire picture when you are so used to a tool, such as jQuery, that bills itself as a solution to all of your browser problems.

It is rarely appropriate to rely exclusively on JavaScript in any form (including through jQuery's API) to style your markup. Cascading Style Sheets exist for this purpose. But that does not mean that there is _never_ an occasion where it is appropriate to dynamically change styles directly through JavaScript. Perhaps you have constructed a web application that allows your users to create their own web custom landing page. Your user desires to to display all secondary headings in italics. To easily do this, you can programmatically add a CSS rule to the document using the `insertRule` method on the `CSSStyleSheet` interface.

{title="adding a style to the document - modern browsers", lang=javascript}
~~~~~~~
stylesheet.insertRule(
  'h2 { font-weight: italic; }', stylesheet.cssRules.length - 1
);
~~~~~~~

The above example will create a new style that will display all `<h2>` elements in italics. The rule will be appended to the end of a stylesheet. The `stylesheet` variable can refer to a `<style>` element we've created on-demand for these sorts of dynamic styles, or even an existing stylesheet imported using a `<link>` tag. If you need to support Internet Explorer 8, you'll have to use `addRule` instead, if it is defined in the browser's implementation of the DOM API.

Using stylesheets or `<style>` elements is almost always the preferred approach over a JavaScript-only solution. Even so, it is often acceptable to take a holistic approach, incorporating JavaScript, HTML, and stylesheets into your solution as the situation dictates. Now that you have a more complete understanding of the possibilities, you are in a better position to make these kinds of decisions correctly in your own projects. The rest of this chapter is dedicated to more specific styling situations. As is customary in Beyond jQuery, I'll use the familiar jQuery approach as a reference, followed by copious web API examples.


## Getting and setting generalized styles

### Using jQuery

%% - jQuery relies heavily on the `style` property


### Without jQuery


## Setting and determining element visibility

### The idiomatic jQuery approach

### The native web approach


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


[csp-mdn]: https://developer.mozilla.org/en-US/docs/Web/Security/CSP/Introducing_Content_Security_Policy

[csp-w3c]: http://www.w3.org/TR/2012/CR-CSP-20121115/

[css1]: http://www.w3.org/TR/REC-CSS1/

[css1-style]: http://www.w3.org/TR/REC-CSS1/#containment-in-html

[css2]: http://www.w3.org/TR/CSS2/

[dom2-cssstyledeclaration]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-CSSStyleDeclaration

[dom2-style]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-ElementCSSInlineStyle

[dry]: http://www.artima.com/intv/dry.html

[html2-link]: http://www.w3.org/MarkUp/html-spec/html-spec_toc.html#SEC5.2.4

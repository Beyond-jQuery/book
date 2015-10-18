# Styling Elements {#styling-elements}

If you're used to using jQuery's `css` method to work with styles in your document, this chapter is for you. I can certainly relate to blind dependence on this magical aspect of the API. Adjusting the dimensions, color, opacity, and any other style imaginable is _really_ easy to do with the help of jQuery. Unfortunately, this simplicity comes at a substantial cost.

jQuery's internal code that backs its easy-to-use CSS API is horrendous in terms of performance. If you value efficiency at all, if you want to provide your users with an optimal experience, you should learn how to properly manipulate and read element styles using the web API. Instead of relying on a "one size fits all" approach, you should choose the leanest route possible by bypassing jQuery's abstraction.

You may continue to rely on jQuery, or get rid of it entirely in favor of a more "natural" programmatic approach. But there are other concepts to be aware of other than which JavaScript method to use. Consider the possibility that JavaScript is not always the best way to adjust styles in your document. In addition to HTML and JavaScript, the browser provides a third valuable tool - stylesheets.

"Beyond jQuery" aims to provide you with a better understanding of the options provided natively by your browser, with each chapter building on your newfound knowledge. In this chapter, you'll learn some new things about working with element styles both with and without JavaScript. You'll learn enough from this chapter to understand when to target elements using CSS rules in stylesheets instead of resorting to JavaScript 100% of the time. Your strong knowledge of [selectors](#finding-elements) and [attributes](#element-attributes), courtesy of the last few chapters, will make this much easier.


## There are three ways to style elements {#three-ways-to-style}

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


{title="styles.css - external stylesheet - all browsers", lang=css}
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

After describing (and demonstrating) several distinct ways to add style to your HTML elements, it's time to examine working with CSS a bit closer. If you are familiar with jQuery (and if you are reading this book, you probably are) then you already know that there is typically one narrow path to adjusting document look and feel when using jQuery. I'll provide a demonstration, for the purposes of reference. But the native route encouraged by the native browser stack is much richer. In this section, you'll see how to _properly_ get styles, and set them dynamically without any assistance from jQuery.

To setup the jQuery and non-jQuery demonstrations below, let's start with a simple HTML fragment:

{title="markup for generalized style demos", lang=html}
~~~~~~~
<button>cookies</button>
<button>ice cream</button>
<button>candy</button>
~~~~~~~

Suppose you would like to style any of the buttons a bit differently after they are clicked (or selected via the keyboard). These buttons should be styled in some way to indicate that they have been selected. I haven't covered event handlers yet (though I will [in a later chapter](#browser-events)) so just assume you a function already exists with the associated button element passed in as a parameter whenever the button is selected. Your job is to fill in the implementation of this function by changing the background and border color of the selected button to blue, and the button text to white.

To demonstrate reading styles (and further demonstrate setting them), consider an element that has already been styled as a box. Whenever this box is clicked, it becomes slightly more opaque, until it disappears entirely. Again, assume you are passed a function whenever the box is clicked. Your job is to make the box 10% more opaque whenever this function is called.


### Using jQuery

jQuery, being a very popular JavaScript library relied upon by far too many developers, has a duty (in my humble opinion) to teach these developers the proper way to adjust element styles. Unfortunately, it fails to do so. Even the [jQuery Learning Center article on styling][jquery-learning-styling] only briefly touches on how to properly style elements, without any real demonstration of this technique at all. The reason for this is simple - idiomatic jQuery is often at odds with best practices. This fact was one of several inspirations for "Beyond jQuery". But, I digress. Let's see how most jQuery-centric developers would solve the problem described above.

{title="adjusting selected button style - jQuery", lang=javascript}
~~~~~~~
function onSelected($selectedButton) {
  $selectedButton.css('color', 'white')
    .css('background-color', 'blue')
    .css('border-color', 'blue');
}
~~~~~~~

When writing styles to elements, the `css` method acts as a wrapper around the `style` property on the `HTMLElement` interface. This is elegant no doubt, but is it really the proper approach? The answer, of course, is no. I covered this in [the previous section](#three-ways-to-style). Granted, the above is not the _only_ solution to this problem using jQuery, but it is the most common one among jQuery developers.

Now, let's examine how one would typically solve the second problem using jQuery:

{title="10% increase in opacity per click of box - jQuery", lang=javascript}
~~~~~~~
function onClicked($clickedBox) {
  var currentOpacity = $clickedBox.css('opacity');

  if (currentOpacity > 0) {
    $clickedBox.css('opacity', currentOpacity - .1);    
  }
}
~~~~~~~

Unfortunately, jQuery's `css` API method is horribly inefficient. Each call to this method to lookup a style requires jQuery to utilize the `getComputedStyle` method on the `window` object, which is completely unnecessary after the first call and adds a notable amount of processing overhead to this solution.


### Without jQuery

The proper way to solve the first problem is to include the CSS rules in an external stylesheet, and trigger these rules using minimal JavaScript. Remember, we are looking to style a button, when it is selected/pressed, to stand out. We can expect a function to be called, with the element passed as a parameter, when the button is pressed.

Let's start by defining our styles for the pressed button in a stylesheet:

{title="styles.css - pressed button styles - all browsers", lang=css}
~~~~~~~
button.selected {
  color: white;
  background-color: blue;
  border-color: blue;
}
~~~~~~~

When the button is pressed, all we need to do is add a CSS class to the element to trigger the styles defined in the styles.css file. Now, we need to implement the function that adds the "selected" class to this button in order to trigger the style rules defined in the stylesheet.

{title="button-handler.js - add class to pressed button - all browsers", lang=javascript}
~~~~~~~~
function onSelected(selectedButton) {
  selectedButton.className += ' selected';
}
~~~~~~~~

What follows is a line to import the CSS file, our button element, _and_ the function that, when called, triggers the style rules on the button.

{title="index.html - define the button, pull in styles and JS - all browsers", lang=html}
~~~~~~~
<link rel="stylesheet" href="styles.css">
<script src="button-handler.js"></script>
<button>demo button</button>
~~~~~~~

This approach has a few advantages. First, it demonstrates a separation of concerns. In other words, styling belongs in stylesheets, JavaScript in JavaScript files, and HTML in HTML files. This separation makes maintenance simpler and potentially less risky. It also ensures that if, for example, the styles are adjusted, the HTML and JavaScript files continue to be cached by the browser. If all of this logic was jammed into a single HTML file, the entire file would have to be re-downloaded by the browser, regardless of the scope of the change.

Another advantage of tying these styles to a CSS class and defining this in an external stylesheet means that these can be easily re-used for other purposes elsewhere in this document, or any other document. The idiomatic jQuery approach leaves us copying and pasting the same styles over and over again, since we are defining them inline.

And the second scenario? Remember, we want to increase the opacity of a box by 10% each time it is clicked. Again, we are given a function that will be called with the box element whenever it is clicked.

{title="10% increase in opacity per click of box - modern browsers", lang=javascript}
~~~~~~~
function onClicked(clickedBox) {
  var currentOpacity = clickedBox.style.opacity ||    
    getComputedStyle(clickedBox, null).getPropertyValue('opacity');

  if (currentOpacity > 0) {
    clickedBox.style.opacity = currentOpacity - 0.1;    
  }
}
~~~~~~~

Our optimized non-jQuery approach is a _little_ more code, but it is [_much_ faster than the idiomatic jQuery solution][jsperf-css]. Here, we are only utilizing the expensive call to [`getComputedStyle`][getcomputedstyle-w3c] when there is no style defined on the element's `style` property. `getComputedStyle` is determines an element's actual style by examining not only the element's `style` property, but also by looking at the document's stylesheets. As a result, this operation can be very intensive, so we avoid it unless absolutely necessary.


## Setting and determining element visibility

Showing and hiding elements is a common problem in web development. These tasks may not be straightforward, but it's often even more complicated to determine, programmatically, if an element is visible or not. Traditionally, element visibility is a confounding problem for developers to deal with. It doesn't _have_ to be this way. There are two ways to do handle element visibility - the way you've always done it (using jQuery), and the correct way (without jQuery). You'll see how shockingly inefficient jQuery is in this context, and how this illustrates why blind faith in this type of software library is dangerous.


### The typical jQuery approach

The upside of using jQuery to show, hide, and determine the visibility of an element, is simplicity. As you'll find out soon, this is, by far, the _only_ upside. But for now, let's focus on this simplicity.

Hiding an showing elements with jQuery is almost always accomplished using the `show` and `hide` API methods, respectively. There's no real need to a fragment of HTML to demonstrate these methods, so let's just dive into a couple examples.

{title="showing and hiding elements - jQuery", lang=javascript}
~~~~~~~
// hide an element
$element.hide();

// show it again
$element.show();
~~~~~~~

None of the above code demands further description or elaboration. But what _does_ need further examination is the underlying code that actually carries out these operations. Unfortunately, both of these methods make use of `window.getComputedStyle`, a method I discussed in the last section. In some cases, particularly with `hide`, `getComputedStyle` may be called multiple times. This has serious performance consequences. Why all is all of this processing power needed simply to hide or show a single DOM element? For the most part, all of the clever and generally unnecessary code underneath these two commonly used API methods is in place solely to handle styling edge cases where it is otherwise difficult to show or hide a targeted element. As I said before, element visibility doesn't _have_ to be a complex problem. We can avoid all of the CPU cycles required by jQuery to hide and show elements simply by adopting a more simplistic approach. More on that in the upcoming "native web approach" section.

What if we need to figure out if a specific element is hidden, or not? Well, jQuery makes this really easy too!

{title="determining element visibility - jQuery", lang=javascript}
~~~~~~~
// is the element visible?
$element.is(':visible');

// conversely, is the element hidden?
$element.is(':hidden');
~~~~~~~

jQuery has decided to invent a new pseudo-class to represent element visibility. How nice. Even the creator of jQuery, John Resig, went on at length about [the amazingness of this new innovative jQuery concoction][resig-selectors]. But just like `show`, `hide`, and the `css` API methods, these two non-standard pseudo-classes are horrifically slow. Again, they delegate to `window.getComputedStyle`, again, sometimes multiple times per invocation. In the next section, I'll outline several non-jQuery methods of showing and hiding elements, as well as determining the visibility of an element. The performance differences between the native and the jQuery methods will be included as well, and the differences will be notable, to say the least.


### The native web approach

The simplicity of hiding, showing, and evaluating the visibility of an element in jQuery is compelling. Though there are serious performance consequences that come with this simplicity. This is the part where you may expect me to say something like "it's a bit more difficult to do all of this without jQuery", or "there's an easy way to solve this problem without jQuery, but it requires use of bleeding edge browsers". In reality, it is _very_ easy to show, hide, and determine element visibility in any browser, without jQuery. jQuery developers may want you to believe that these are complex problems to solve, and you _need_ jQuery to solve them, but none of this is true. In this section, I'll demonstrate some simple conventions that will result in simple solutions.

There are _many_ ways to hide an element. Some of the more unconventional methods include setting the element's `opacity` to 0, or setting the `position` to "absolute" and positioning it outside the visible page. These and other similar approaches may be effective, but they are generally considered to be "kludgey". As a result, it is generally discouraged to use these methods when attempting to hide an element. Please don't do this; there are better ways.

A more reasonable approach involves setting the element's `display` style property to "none". As you have already learned, there are a number of different ways to adjust an element's style. But you have also learned that the best approach is to define this style in an external stylesheet. So, perhaps the best solution would be to define a custom CSS class or attribute in your stylesheet, include a `display: none` style for these selector, and then add the associated class or attribute to this element when it needs to be hidden.

So, which should we choose - an attribute or a CSS class? Does it really matter? The W3C HTML5 specification defines a [`hidden`](#hidden-html5) [boolean attribute](#boolean-attributes), which, as you might expect, allows you to hide an element simply by adding this attribute to the element. Not only does this standardized attribute allow you to easily hide an element, it also enhances the semantics of your markup and [provides a useful cue to _all_ screen readers][hidden-accessiblity]. Yes, it even makes your elements more accessible. And since `hidden` is part of a formal specification, it isn't just a convention, it's represents _the_ correct way to deal with element visibility.

At this point, you are probably checking to see which browsers support this attribute. Let me save you the trouble - not all of them. In fact, the `hidden` attribute wasn't first supported by Microsoft until Internet Explorer 11. Luckily, the polyfill for the standardized `hidden` attribute is _unbelievably simple and elegant_ - simply add the following style to your global stylesheet:

{title="polyfill for standardized `hidden` attribute - all browsers", lang=css}
~~~~~~~
[hidden] {
  display: none;
}
~~~~~~~

And this means you can hide _any_ element in _any_ browser with the following line of JavaScript:

{title="hide an element - all browsers", lang=javascript}
~~~~~~~
element.setAttribute('hidden', '');
~~~~~~~

Hiding elements couldn't possibly be simpler, more elegant, or more performant. This approach is incredibly faster than jQuery's `hide()` API method. In fact, [jQuery's `hide()` method is more than 25 times slower][jquery-hide-jsperf]! There is _no_ reason to continue using jQuery to hide elements.

Since the simplest and most performant method of hiding an element involves adding an attribute, you may not be surprised to learn that you can show the same element simply by _removing_ that same attribute.

{title="showing an element that was previously hidden w/ the `hidden` attribute - all browsers", lang=javascript}
~~~~~~~
element.removeAttribute('hidden');
~~~~~~~

Since we're following this convention - add an attribute to hide and element and remove it to show the element again - determining the visibility of the element is simple. All we need to do is check the element for the existence of this attribute, which is a trivial operation in all notable browsers.

{title="determining if an element is hidden - all modern browsers + IE8", lang=javascript}
~~~~~~~
// the element is hidden if this returns true
element.hasAttribute('hidden');
~~~~~~~

Yes, it's really that easy.


## Determining width and height of any element

Before I review how jQuery allows you to check the width and height of an element, _and_ how you can easily do this _without_ using a DOM abstraction, you'll need to understand some basic concepts. The most critical specification required to intelligently calculate the width and height of any element is [the box model][boxmodel-w3c].

Every element is a box. One more time: _every element is a box_. This is simple, yet very surprising for many web developers to hear. Once you get over the initial shock of this realization, the next step is to understand how an element's box is divided up. This is called the box model. Let's start by looking at a drawing of the box model from the World Wide Web Consortium's CSS 2.1 specification:

{#box-model}
![A diagram of the box model. Copyright 2015 W3C. License available at http://www.w3.org/Consortium/Legal/2015/copyright-software-and-document](images/boxdim.png)

As you can see, an element, which again is a _box_, is made up of four "layers" - content, padding, border, and margin. Simply put, an element's content, padding, and border are used to determine its height and width. Margins are not considered to be part of an element's "dimensions" - they simply push other elements away instead of influencing the element's height and width. How you measure width and height largely depends on which of the first three layers you care about. Generally speaking, an element's dimensions can take into account a subset of these three layers - content and padding - or all three layers. jQuery takes a different approach and only considers the content dimensions. More on that next.

### Examining an element using jQuery

Just as with the web API - which I will describe in the following section - there are _many_ ways to discover the dimensions of an element using jQuery's API. You may already be aware of some, or even all of these methods. What you may _not_ be aware of is the horrific performance associated with _all_ of jQuery's built-in element dimension methods - most developers are not. And why should you? The methods are so simple and elegant, the possibility that you are paying a substantial penalty in terms of performance is not often a concern. You probably trust that this critical library is not degrading the efficiency of your application in any noticeable way, but you would be _wrong_.

The most visible API methods are `width()` and `height()`. Remember the [box model diagram](#box-model)? These two jQuery methods only measure the "content" portion of an element's box. This _sounds_ like a reasonable behavior, but it is arguably bizarre, since content only represents a portion of an element's actual width and height. Remember that margin is the only element of the box model that does not directly affect the width and height of an element. Also remember that jQuery isn't magic - it must delegate to the web API for _all_ of it's API methods. And the web API does not provide a simple way to determine the dimensions of an element's content. So, jQuery must perform some unpleasant operations in order to determine these values, sacrificing performance as a result. When I demonstrate how to calculate the width and height of an element using the web API in the next section, I'll show you just how relatively inefficient jQuery's `width()` and `height()` are.

So we already know that `width()` and `height()` only report the dimensions of an element's content. What if you want to include the padding and border segments of the box as well? jQuery has `outerWidth()` and `outerHeight()` methods that include content, padding, _and_ border when reporting the width/height. To include only content and padding, `innerWidth()` and `innerHeight()` methods are provided as well. While all of these API methods have close matches in the web API, jQuery's implementation is substantially slower, as you will see in the next section.


### Options natively provided by the browser

While jQuery's `width` and `height` are popular methods, there is no similar pair of methods or properties to be found in any web specification. The appeal of these methods is likely tied to their suggestive names. After demonstrating some of the standard properties offered by the web API to check element dimensions, I'll show you how you can replicate jQuery's `width()` and `height()` methods without the huge performance penalty.

To better illustrate the code in this section, I'll start open with a simple element that takes up space in all four segments of [the box model](#box-model):

{title="element for us to use when discussing web API width/height", lang=html}
~~~~~~~
<style>
  .box {
    padding: 10px;
    margin: 5px;
    border: 3px solid;
    display: inline-block;
  }
</style>
<span class="box">a box</span>
~~~~~~~


#### Width & height of content + padding

In order to obtain the width or height of the above box, only considering the content and padding values, we can use the [`clientWidth`][cssom-clientwidth] and [`clientHeight`][cssom-clientheight] properties found on the `Element` interface.

These properties were first defined in the [Cascading Style Sheet Object Model (CSSOM) View specification][cssom], drafted by the W3C. As of 2015, the CSSOM spec is not yet a recommended standard, in fact it is only a working draft. But these two `Element` properties, along with many of the other items represented in this specification, have been supported by browsers for a very long time. For example, the `Element.clientWidth` and `Element.clientHeight` properties are supported all the way back to Internet Explorer 6, yet they are only currently defined in this working draft spec. This seems a bit strange, doesn't it? Indeed it is, but the CSSOM spec is a special one. It exists mostly to codify and formally standardize longstanding CSS-related browser behaviors. `Element.clientWidth` and `Element.clientHeight` are two such examples, but you will see others in this section as well.

So what do `clientWidth` and `clientHeight` return on the `<span>` in our markup above?

{title="find width/height of content + padding - web API - modern browsers + IE8", lang=javascript}
~~~~~~~
// returns 38
document.querySelector('.box').clientHeight;

// returns 55
document.querySelector('.box').clientWidth;
~~~~~~~

Note that the above return values may vary slightly between browsers, as default fonts and styling varies slightly between browsers, which will ultimately lead to a slight variance in size of the element's content. This is to be expected.

There is something else at play here that you may not be aware of. Notice the `display: inline-block` style attached to our `<span>` element? Remove it and check the return values of `clientWidth` and `clientHeight` again. Without this style, both of these properties report a value of `0`. By default, all browsers render `<span>` elements as `display: inline`, and inline elements will _always_ report `0` as their `clientWidth` & `clientHeight`. Keep this in mind when using these properties. Note that floating a default inline element will _also_ allow you to calculate width and height this way.

For comparison, jQuery's `width()` and `height()` methods return `35` and `18`, respectively. Remember that these methods _only_ consider the element's content, ignoring padding, border, and margin.


#### Width & height of content + padding + border

And what if you need to include the border when reporting the width and height of an element? That is: content, padding, and border. Simple, use [`HTMLElement.offsetWidth`][cssom-offsetwidth] and [`HTMLElement.offsetHeight`][cssom-offsetheight]. These properties have also long been implemented by browsers, but only first brought into a formal specification in the CSSOM View standard.

~~~~~~~
// returns 44
document.querySelector('.box').offsetHeight;

// returns 61
document.querySelector('.box').offsetWidth;
~~~~~~~

As expected, these values are a bit larger than what `clientHeight` and `clientWidth` report since we are also taking border into account. In fact, each value is exactly 6 pixels larger. This is expected due to a border of 3 pixels on each side, defined in our `<style>` element.

Again, the return values above may vary _slightly_ between browsers due to the way browsers style element content. Also, the `display: inline-block` is not needed for `offsetHeight` and `offsetWidth` - they will _not_ report a zero height and width for inline elements.


#### Width & height of content only

%% getComputedStyle + clientWidth/height for jQuery width/height
%% getBoundingClientRect for fractional w/h


#### Performance comparisons
%% horrific performance of jQuery https://jsperf.com/offsetwidth-vs-jquery-width


## Reading and updating scroll positions

### jQuery

### The web API

%% scrollIntoView(), scroll() / scrollTo(), scrollTop / scrollLeft, scrollY / scrollX

#### Absolute scrolling

#### Relative scrolling


[box-model-w3c]: http://www.w3.org/TR/CSS21/box.html

[csp-mdn]: https://developer.mozilla.org/en-US/docs/Web/Security/CSP/Introducing_Content_Security_Policy

[csp-w3c]: http://www.w3.org/TR/2012/CR-CSP-20121115/

[css1]: http://www.w3.org/TR/REC-CSS1/

[css1-style]: http://www.w3.org/TR/REC-CSS1/#containment-in-html

[css2]: http://www.w3.org/TR/CSS2/

[cssom]: http://www.w3.org/TR/cssom-view/

[cssom-clientheight]: http://www.w3.org/TR/cssom-view/#dom-element-clientheight

[cssom-clientwidth]: http://www.w3.org/TR/cssom-view/#dom-element-clientwidth

[cssom-offsetheight]: http://www.w3.org/TR/cssom-view/#dom-htmlelement-offsetheight

[cssom-offsetwidth]: http://www.w3.org/TR/cssom-view/#dom-htmlelement-offsetwidth

[dom2-cssstyledeclaration]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-CSSStyleDeclaration

[dom2-style]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-ElementCSSInlineStyle

[dry]: http://www.artima.com/intv/dry.html

[getcomputedstyle-w3c]: http://www.w3.org/TR/DOM-Level-2-Style/css.html#CSS-CSSview-getComputedStyle

[hidden-accessiblity]: http://www.html5accessibility.com/tests/hidden2013.html

[hidden-html5]: http://www.w3.org/TR/html5/editing.html#the-hidden-attribute

[html2-link]: http://www.w3.org/MarkUp/html-spec/html-spec_toc.html#SEC5.2.4

[jquery-hide-jsperf]: http://jsperf.com/jquery-hide-vs-setattribute-hidden

[jquery-learning-styling]: https://learn.jquery.com/using-jquery-core/css-styling-dimensions/

[jsperf-css]: http://jsperf.com/jquery-css-vs-optimized-non-jquery-approach3

[resig-selectors]: http://ejohn.org/blog/selectors-that-people-actually-use/

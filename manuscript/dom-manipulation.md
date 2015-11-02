# DOM Manipulation

One of the most confusing and misunderstood aspects of the web API pertains to DOM manipulation. No doubt, you are already used to working with DOM elements using jQuery. But is it necessary to continue depending on a library in this regard? In this eight chapter of Beyond jQuery, I'll show you how to create, update, and move elements and element content _without_ any help from third-party code. You'll come to appreciate how easy it is to work with the DOM in virtually all browsers.


## The DOM: A central component of web development

Most likely you've heard _about_ the DOM. It's a common term in the world of web development. But maybe the DOM is still a mostly mysterious word to you. What exactly _is_ the DOM, and why is it so important? How and why does jQuery abstract the DOM API away from developers?

As stated in one of the [early W3C-maintained documents][w3c-dom-article], "The history of the Document Object Model, known as the DOM, is tightly coupled with the beginning of the JavaScript and JScript scripting languages." This model expresses an API, commonly implemented in JavaScript for use in web browsers, that allows programmatic access to an HTML document. In addition to working with [attributes](#element-attributes), [selecting elements](#finding-elements), and [storing data](#html-data), the DOM API also provides a way to create new content, remove elements, _and_ move existing elements around the document. These specific aspects of the DOM API are the primary focus of this chapter.


### jQuery exists because of the DOM API

Ok, it's not the _only_ reason why jQuery was created, but certainly one of _the_ reasons. John Resig, the creator of jQuery, famously called the DOM a "mess" in [a 2009 presentation at Yahoo][resig-mess-yahoo]. So this perhaps gives us some insight into one of the problems jQuery aims to solve. A few years before Resig's talk, jQuery 1.0 was released (2006). This version of jQuery included about 25 DOM-manipulation-specific methods. This accounts for about 17% of the entire API. Many other methods abstract various DOM-related behaviors and functions as well. Even as the API has grown to handle all aspects of the web API, DOM manipulation functions still account for 15% of the API.

Sure, jQuery provides some elegant solutions to working with the DOM. But are they really necessary? Of course not! Is the DOM still "broken"? Not in my opinion. The DOM API may have some rough edges, but it is fairly easy to use and quite reliable. You _can_ painlessly manipulate the DOM yourself without the help of jQuery.


### The DOM API is not broken, it's just misunderstood

In Resig's talk at Yahoo, mentioned previously, he said "Nearly every DOM method is broken in some way, in some browser." While this is a bit hyperbolic, it may have had _some_ truth to it back in 2009. The current age of browsers paints a much different picture though. While browsers still have bugs, this is true of all software, [even jQuery][jquery-issues]. The jQuery team already apparently knows that the library is no longer useful as a shield from browser bugs, as is evident in [their responses to issues that appear to be browser-specific][methvin-bug-dodge].

One of the major goals of jQuery has always been to shield developers from the DOM API. Why? Because it historically _has_ been a mess. It's also seen as a muddled path to follow when attempting to programmatically update a page's markup. This has a bit of truth to it, but only because many developers have not taken the time to learn the DOM API. As you'll see throughout this chapter, working with the DOM is surprisingly simple and well supported throughout all popular browsers. It's hard to argue with the convenience offered by jQuery API. But you can make better use of jQuery, should you continue to use it, once you have a better understanding of the underlying DOM API methods that jQuery itself makes use of.


## Moving and copying elements

This chapter is all about "pure" DOM manipulation, that is, moving, copying, and creating elements. In this first part, I'm going to focus on moving and copying existing elements. You'll learn how to insert elements anywhere in the DOM, change the order of adjacent elements, as well as clone an element. I'll demonstrate methods present on the [`Document` interface][mdn-document], [`Element`][mdn-element], and [`Node`][mdn-node] interfaces, among others. You'll see how basic DOM operations have been executed with jQuery, followed by explanations and demonstrations of the same tasks utilizing the DOM API alone.


### Moving elements around the DOM

I'm going to start this section out with one of my patented (pending) sample documents. In order to focus primarily on the functionality of the demonstrable methods, I'll keep this simple and straightforward. Admittedly, this results in a somewhat contrived example, but I feel this will easily allow us to compare and contrast jQuery with the DOM API in the context of DOM manipulation.

Our super-simple document consists of a few different categories and attributes of ice cream: flavors, types, and a section of unassigned types and flavors. This document represents some choices for customers of an ice-cream shop. Using this markup, we're going to solve several "problems", first with jQuery, and then with the plain 'ole DOM API.

The first challenge involves reordering the flavors and types in descending order, based on their popularity. Chocolate is most popular flavor, followed by vanilla and strawberry. Also, custard is the most popular type, followed by frozen yogurt and Italian ice. We'll have to change the order of the list items to reflect these facts.

Second, we really want to present our readers with the types of ice cream _first_, followed by the flavors. The current order, which includes _flavors_ first, is known to be less-than-ideal, as our users want to be informed of the types first before deciding on flavors.

Finally, we need to take the items in the "unassigned" section, and assign them to the proper category. "Rocky road" is a flavor, which is less popular than vanilla, but more popular that strawberry. And "gelato" is a type, the least popular of the bunch.

{title="moving elements demonstration document - original", lang=html}
~~~~~~~
<body>
  <h2>Flavors</h2>
  <ul class="flavors">
    <li>chocolate</li>
    <li>strawberry</li>
    <li>vanilla</li>
  </ul>

  <h2>Types</h2>
  <ul class="types">
    <li>frozen yogurt</li>
    <li>Italian ice</li>
    <li>custard</li>
  </ul>

  <ul class="unassigned">
    <li>rocky road</li>
    <li>gelato</li>
  </ul>
</body>
~~~~~~~

After solving the problems described above, our document should look like this:

{title="moving elements demonstration document - after reordering", lang=html}
~~~~~~~
<body>
  <h2>Types</h2>
  <ul class="types">
    <li>frozen yogurt</li>
    <li>Italian ice</li>
    <li>custard</li>
    <li>gelato</li>
  </ul>

  <h2>Flavors</h2>
  <ul class="flavors">
    <li>chocolate</li>
    <li>vanilla</li>
    <li>rocky road</li>
    <li>strawberry</li>
  </ul>

  <ul class="unassigned">
  </ul>
</body>
~~~~~~~


#### Moving elements using jQuery

%% $.append / $.prepend


#### The DOM API's solution to reordering elements

%% insertBefore
%% appendChild


### Making copies of elements

%% $.clone
%% cloneNode


## Creating your own elements and content

%% add some new flavors and types
%% remove some existing flavors and types
%% fix typos in flavors or types
%% create a new section


### Creating and deleting elements

%% add some new flavors and types
%% remove some existing flavors and types

%% $('<div>')
%% document.createElement

%% $.remove
%% remove
%% removeChild
%% replaceChild


### Text content

%% fix typos in flavors or types

%% $.text
%% textContent, innerText, innerHTML


### Rich content

%% create a new section

%% $.html
%% document: outerHTML, innerHTML, write/writeln
%% insertAdjacentHTML


[mdn-document]: https://developer.mozilla.org/en-US/docs/Web/API/Document

[mdn-element]: https://developer.mozilla.org/en-US/docs/Web/API/Element

[mdn-node]: https://developer.mozilla.org/en-US/docs/Web/API/Node

[methvin-bug-dodge]: https://github.com/jquery/jquery/issues/2679#issuecomment-152289474

[jquery-issues]: https://github.com/jquery/jquery/issues

[resig-mess-yahoo]: http://ejohn.org/blog/the-dom-is-a-mess/

[w3c-dom-article]: http://www.w3.org/2002/07/26-dom-article.html

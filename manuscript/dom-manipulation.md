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

The first challenge involves reordering the flavors and types in descending order, based on their popularity. Chocolate is most popular flavor, followed by vanilla and strawberry. The types section is in the correct order though. We'll have to change the order of the flavors list items to reflect the popularity.

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
    <li>custard</li>
    <li>Italian ice</li>
  </ul>

  <ul class="unassigned">
    <li>rocky road</li>
    <li>gelato</li>
  </ul>
</body>
~~~~~~~

After solving the problems described above, our document should look like this:

{id="moving-elements-markup-result", title="moving elements demonstration document - original", lang=html}
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

In order to properly order the flavors, "vanilla" must be moved after "chocolate". To accomplish this, we must make use of jQuery's `after()` API method.

{title="Move an element after another element - jQuery", lang=javascript}
~~~~~~~
var $flavors = $('.flavors'),
    $chocolate = $flavors.find('li').eq(0),
    $vanilla = $flavors.find('li').eq(2);

$chocolate.after($vanilla);
~~~~~~~

For our second challenge, we have to move the "types" section _and_ the heading (`<h2>`) for the "types" section before the "flavors" section. We can take advantage of the fact that this means the heading and section must by the first set of children of the `<body>` element. First, we "prepend" the "types" heading to the `<body>` using the `prependTo()` method, and then insert the "types" section after the newly moved heading, again using jQuery's `after()` method.

{title="Move elements after and as the first child of another element - jQuery", lang=javascript}
~~~~~~~
var $typesHeading = $('h2').eq(1);

$typesHeading.prependTo('body');
$typesHeading.after($('.types'));
~~~~~~~

Finally, we need to move the unassigned "rocky road" just above "strawberry" in the flavors section, and "gelato" to the end of the "types" section. For the first move, we can again use jQuery's `after()` method. For the second move, we will use the `appendTo` method on the "gelato" element to insert it as the last child of the "types" section.

{title="Move elements after and as the last child of another element - jQuery", lang=javascript}
~~~~~~~
var $unassigned = $('.unassigned'),
    $rockyRoad = $unassigned.find('li').eq(0),
    $gelato = $unassigned.find('li').eq(1);

$vanilla.after($rockyRoad);
$gelato.appendTo($('.types'));
~~~~~~~

None of the above solutions are particularly elegant or intuitive. It's certainly possible to come up with more attractive examples to solve these problems, but I would expect this attempt to be common among jQuery developers. We also could have made use of some of jQuery's proprietary psuedo-classes, such as `:first` and `:last`, but [we already learned how inefficient those options are](#element-categories-and-modifiers).


#### The DOM API's solution to reordering elements

In order to make the proper adjustments to our ice cream store page _without_ jQuery, I'm going to have to introduce two new DOM API methods. You'll also see a number of selectors and other DOM API methods discussed in the [Finding HTML Elements chapter](#finding-elements). You'll also be pleasantly surprised to discover that _all_ of the code in this section works in all modern browsers _and_ Internet Explorer 8 as well! Before we start, the eye-rolling nature of this example ice cream store markup is not lost on me, but it allows me to succinctly demonstrate a number of DOM manipulation operations without getting bogged down in details unrelated to the problem at hand. That said, let's get started!

Remember that our first task is to move the "vanilla" element before the "strawberry" element. To accomplish this, we can make use of [the `insertBefore` method][dom2core-insertbefore], which was added to the `Node` interface as part of W3C's DOM Level 2 Core specification. This method, as you might imagine, allows us to move one element just before another in the DOM. And because this is available on the `Node` interface, we have the power to move anything in the DOM, even a `Text` or `Comment` node! Take a look at how we move this element below. I'll explain what's going on immediately after the code fragment.

{title="Move an element after another element - DOM API - all modern browsers + IE8", lang=javascript}
~~~~~~~
var flavors = document.querySelector('.flavors'),
    strawberry = flavors.children[1],
    vanilla = flavors.children[2];

flavors.insertBefore(vanilla, strawberry);
~~~~~~~

At the top of the above code, I'm simply selecting elements needed by our move operation. The last line is the most important one. Since the `insertBefore` method is defined on the `Node` object's `prototype`, we must call `insertBefore` on an DOM object that implements this interface. In fact, this element _must_ be a parent element of the `Node` we are moving. Since we are moving the "vanilla" `<li>` element, we can use its parent, the "flavors" `<ul>`.

The first parameter passed to `insertBefore` is the element we want to relocate - the "vanilla" list item. The _second_ parameter is the "reference node". That is, the `Node` that will become the next sibling of our target element (the "vanilla" `<li>`) _after_ the move operation. Since we want to move "vanilla" before "strawberry", the "strawberry" `<li>` is our reference node.

So we've reordered our flavors, but we still need to move the flavors heading and list to the top of our document. We can easily accomplish this goal with the `insertBefore` method as well.

{title="Move elements after and as the first child of another element - DOM API - all modern browsers + IE8", lang=javascript}
~~~~~~~
var headings = document.querySelectorAll('h2'),
    flavorsHeading = headings[0],
    typesHeading = headings[1],
    typesList = document.querySelector('.types');

document.body.insertBefore(typesHeading, flavorsHeading);
document.body.insertBefore(typesList, flavorsHeading);
~~~~~~~

The meat of our logic is contained in the final two lines of the above code. First, we're moving the "types" `<h2>` to the top of our document. The parent of this heading is the `<body>` element, which we can easily select using `document.body`. Our target element is, of course, the "types" heading. We want to move this just before the "flavors" `<h2>`, so that becomes our reference element.

The second `insertBefore` moves the `<ul>` of ice cream types after the recently moved heading. Again `<body>` is our parent element. Since we need to move this list before the "flavors" heading, that is again our reference node.

Our final task is to move the unassigned elements into their respective lists. To accomplish this, we'll again make use of `insertBefore`, but you'll also see a new method in action. The W3C DOM Level 1 specification, which is quite an old spec, first defined an [`appendChild` method on the `Node` interface][domlevel1-appendchild]. This method will be of some use to us as we wrap up our exercise.

{title="Move elements after and as the last child of another element - DOM API - all modern browsers + IE8", lang=javascript}
~~~~~~~
flavors.insertBefore(document.querySelector('.unassigned > li'),
  strawberry);

document.querySelector('.types').appendChild(
  document.querySelector('.unassigned > li'));
~~~~~~~

In the first line, we're moving the "rocky road" element from the unassigned list into the flavors list. The flavors list is our parent element, as expected. The target is the first list item child of the unassigned list, which happens to be the "rocky road" `<li>`. And the reference node is the strawberry item in the flavors list, since we want to move "rocky road" before this element.

We want to move the "gelato" list item to the _end_ of the types list. The simplest way to do this is to make use of `appendChild`. As the `insertBefore` method, `appendChild` also expects to be called on the parent of the node we plan to move. This parent is the types list. The `appendChild` method only takes one argument - the element to move to the last child of the parent element. At this point, the "gelato" item is the first `<li>` child in the unassigned list, so we can use the same selector as used to locate the target element in our `insertBefore` statement.

That was all surprisingly easy, wasn't it? The DOM API may not be as scary as many make it out to be!


### Making copies of elements

To demonstrate the various ways to clone elements using jQuery and the DOM API, consider the following markup:

{title="node cloning HTML", lang=html}
~~~~~~~
<ol class="numbers">
  <li>one</li>
  </li>two</li>
</ol>
~~~~~~~

The DOM API offers a method to clone the `<ol>` _and_ its children, as well as a way to clone _only_ `<ol>` and _not_ any of its children/content. The former is called a "deep clone", and the latter a "shallow clone". jQuery _only_ offers a way to deep clone.

In jQuery-land, we must make use of `$.clone()`:

{title="cloning elements - jQuery", lang=javascript}
~~~~~~~
// deep clone: return value is an exact copy
$('.numbers').clone();
~~~~~~~

You can optionally pass boolean parameters to `clone()` above if you'd like jQuery to clone any data and event listeners on the element. But be warned that jQuery will only copy event listeners and data attached to the element via jQuery. Any listeners and data added outside of jQuery's API will be lost.

The DOM API provides an similarly named method, `cloneNode`, available on the `Node` interface. [It was first standardized as part of DOM Level 2 Core][dom2core-clonenode], which became a W3C recommendation back in 2000. As a result, `cloneNode` is supported in _any_ browser. Our example below is limited Internet Explorer 8 and up (though this is hardly a problematic limitation) due to my use of `querySelector`.

{title="cloning elements - DOM API - all modern browsers + IE8", lang=javascript}
~~~~~~~
// shallow clone: return value only the empty <ol>
document.querySelector('.numbers').cloneNode();

// deep clone: return value is an exact copy
document.querySelector('.numbers').cloneNode(true);
~~~~~~~

In both cases, the element copies will contain _everything_ defined in the markup, even the class names, and any other attributes such as inline styles. Event listeners are _not_ included with the copy, nor are any properties exclusively set on the element's JavaScript object representation. In other words, `cloneNode` copies _only_ what you see: markup.

Whether you are using jQuery or the DOM API, the copy created by `cloneNode` is _not_ added to the document for you. You will need to do this yourself using one of the methods demonstrated earlier in this section.


## Composing your own elements

Now that we've explored moving and coping elements, how about creating and removing them? This section explores just that. You'll see how these common problems have been solved with jQuery, and how you can solve them just as easily with the DOM API. Just like the previous section, all DOM API code below will work in all modern browsers _and_ Internet Explorer 8. In essence, this covers all browsers in use today.

To best demonstrate all of the concepts outlined in this final section, I'll build upon the [modified example document from the previous section](#moving-elements-markup-result) used to demonstrate moving elements. Using both jQuery and the bare DOM API, I'll show you how to perform various operations on our example document, such as:

1. Add some new ice cream flavors.
2. Remove some existing types.
3. Make simple text adjustments to our document.
4. Create a new section to further classify our ice cream.


### Creating and deleting elements

Suppose we have a couple new flavors to add to our list: pistachio and neapolitan. These of course belong in the "flavors" section. In order to accomplish this task, we'll need to create two new `<li>` elements with `Text` `Node`s that contain the names of these two new flavors. We'll just add the flavors to the end of the list. We also want to remove the "gelato" type from the end of the list of types, since we no longer sell gelato ice cream.

Creating elements is pretty easy with jQuery, and due to chaining we can add both elements in two lines:

{title="add elements to a document - jQuery", lang=javascript}
~~~~~~~
var $flavors = $('.flavors');

// add two new flavors
$('<li>pistachio</li>').appendTo($flavors);
$('<li>neapolitan</li>').appendTo($flavors);
~~~~~~~

Removing an element isn't very difficult either:

{title="remove an element from a document - jQuery", lang=javascript}
~~~~~~~
// remove the "gelato" type
$('.types li:last').remove();
~~~~~~~

Above, we've made use of a CSS selector, partially proprietary. We're removing for the last `<li>` underneath the element with a "types" CSS class. This happens to be our "gelato" type. The `:last` pseduo-class is specific to jQuery, and as such is not particularly performant. There _is_ a native CSS pseduo-class we could use, which you will see in a moment. But most jQuery developers likely don't know that it exists, since the jQuery API provides this alternative.

How can achieve the same results with the DOM API? Depending on desired browser support, we may have several options.While newer browsers _may_ have more elegant options than older ones, this is not always the case and these operations are all relatively simple in _all_ modern browsers (and even older ones) without relying on jQuery.

We can add our two new flavors to the end of the "flavors" list in one line each as well, although the lines are a _bit_ longer than the jQuery solution above. Again, our use of `querySelector` is the only reason for the Internet Explorer 8 limitation.

{title="add elements to a document - DOM API - all modern browsers + IE8", lang=javascript}
~~~~~~~
var flavors = document.querySelector('.flavors');

// add two new flavors
flavors.insertAdjacentHTML('beforeend', '<li>pistachio</li>')
flavors.insertAdjacentHTML('beforeend', '<li>neapolitan</li>')
~~~~~~~

Above, I'm using [the `insertAdjacentHTML` method][domparsing-insertadjacent] present on the `Element` interface prototype. While this method has likely existed in browsers for many years, it was only first standardized in the [W3C's DOM Parsing and Serialization specification][domparsing], which was drafted in 2014.

What about removing "gelato" from our list of types? In the newest available browsers, we have the most elegant solution:

{title="remove an element from a document - DOM API - Microsoft Edge, Chrome, Firefox, Safari 7 (desktop only)", lang=javascript}
~~~~~~~
// remove the "gelato" type
document.querySelector('.types li:last-child').remove();
~~~~~~~

The above code is _very_ similar to the jQuery solution, with a couple noticeable differences. First, I am of course using `querySelector` to locate the element to remove. Second, I'm making use of [the `:last-child` CSS3 pseudo-class][css3-lastchild] selector. The `remove()` method, present on the `ChildNode` interface, is relatively new and only supported in Microsoft Edge, Chrome, Firefox, and Safari 7. It is not supported on any versions of Internet Explorer, nor is it available on Apple iOS browsers. This method was first defined by the [WHATWG](#whatwg-vs-w3c) as [part of their DOM living standard][dom-remove]. This method in particular is our limiting factor in terms of browser support.

Luckily, we have a solution that covers _all_ modern browsers, which requires only a little more code:

{title="remove an element from a document - DOM API - all modern browsers", lang=javascript}
~~~~~~~
var gelato = document.querySelector('.types li:last-child');

// remove the "gelato" type
gelato.parentNode.removeChild(gelato);
~~~~~~~

I've replaced `ChildNode.remove()` with `Node.removeChild`, which [has existed since DOM Level 1 Core][domlevel1-removechild], so it is supported in all browsers. In order to remove a child node, of course we need to access the parent first. Luckily, it's really easy to do this, as you learned in [the Finding HTML Elements chapter](#parents-and-children-webapi). In this instance, the code that limits us to modern browsers is the `:last-child` CSS3 pseudo-class, which isn't available in Internet Explorer 8.

In order to support IE8, you'll have to replace the selector with something like `document.querySelectorAll('.types li')[3]`. And if you don't want to hard-code the index of the gelato element, you'll have to move the result of the `querySelectorAll` into a variable, and access the last element in the returned collection by examining its `length` property.


### Text content

There are two workflows to address in terms of element text: updating and parsing. While jQuery provides one specific method to accomplish both of these tasks, the DOM API offers two - both with different behaviors that accommodate different needs. In this section, I'll demonstrate jQuery's `text()` method, the native `textContent` property, _and_ the native `innerText` property. You'll see how each of these differ as we make changes to [our document of ice cream types and flavors](#moving-elements-markup-result) and then output the resulting document as text.

First, let's examine jQuery's `text()` method, which allows us to both read and update text in a document. Notice that one of our types "Italian ice", starts with a capital letter. None of the other types or flavors share this trait. Even though "Italian" is a proper adjective and normally _should_ start with a capital "t", let's modify it to be consistent with the case of the rest of our types and flavors.

{title="update text - jQuery", lang=javascript}
~~~~~~~
$('.types li').eq(1).text('italian ice');
~~~~~~~

As you probably already know, the text of an element can be updated simply by passing the new text as a parameter of the `text()` method. This is exactly what I have done above in order to normalize the case of this type of ice cream. What would our modified document look like if we output it using jQuery's `text()` method?

{title="output document as text using $('body').text()"}
~~~~~~~
"
  Types

    frozen yogurt
    italian ice
    custard
    gelato


  Flavors

    chocolate
    vanilla
    rocky road
    strawberry




"
~~~~~~~

The quotation marks have been added to show where the output starts and ends. They are not part of the actual text. Note that this output reflects the structure of our _markup_. This is noticeable by examining the indentation of the text as well as the line breaks at the end of the document. The series of line breaks before the output ends account for the empty "unassigned" list. You'll see in a moment how this output mirrors the output of _one_ of the two native properties that I'm going to cover next.



%% textContent, innerText


### Rich content

%% create a new section

%% $.html
%% document: outerHTML, innerHTML, write/writeln
%% insertAdjacentHTML


[css3-lastchild]: http://www.w3.org/TR/css3-selectors/#last-child-pseudo

[dom-remove]: https://dom.spec.whatwg.org/#dom-childnode-remove

[domlevel1-appendchild]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html#ID-184E7107

[domlevel1-removechild]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html#method-removeChild

[dom2core-clonenode]: http://www.w3.org/TR/DOM-Level-2-Core/core.html#ID-3A0ED0A4

[dom2core-insertbefore]: http://www.w3.org/TR/2000/REC-DOM-Level-2-Core-20001113/core.html#ID-952280727

[domparsing]: https://w3c.github.io/DOM-Parsing/

[domparsing-insertadjacent]: https://w3c.github.io/DOM-Parsing/#widl-Element-insertAdjacentHTML-void-DOMString-position-DOMString-text

[mdn-document]: https://developer.mozilla.org/en-US/docs/Web/API/Document

[mdn-element]: https://developer.mozilla.org/en-US/docs/Web/API/Element

[mdn-node]: https://developer.mozilla.org/en-US/docs/Web/API/Node

[methvin-bug-dodge]: https://github.com/jquery/jquery/issues/2679#issuecomment-152289474

[jquery-issues]: https://github.com/jquery/jquery/issues

[resig-mess-yahoo]: http://ejohn.org/blog/the-dom-is-a-mess/

[w3c-dom-article]: http://www.w3.org/2002/07/26-dom-article.html

# Finding HTML Elements {#finding-elements}

How many times have you come across a project that uses jQuery simply to perform seemingly trivial element selection? How many times have _you_ written `$('#myElement')`, or `$('.myElement')`? If you have depended on jQuery for most (or all) of your projects, you may not be aware of the fact that you don't _need_ jQuery to select elements! This task is fairly straightforward with the help of the plain 'ole boring web API. Much like jQuery, there are abundant examples (such as this book) that demonstrate how to properly harness the power of the browser to swiftly select any and all elements in your document. None of this is a well-kept secret, but the pervasiveness of jQuery has lead many to believe that the _only_ sane way to find elements is with the help of the almighty dollar sign. Nothing could be further from the truth, and all of this will become clear as you read on.

All of the methods described here to select elements are supported in _all_ [modern browsers](#modern-browsers). In fact, many are supported in [ancient browsers](#ancient-browsers) as well! In other words, unless you are supporting an aging legacy web application, even the most complex native solutions for selecting elements are available to you without the help of _any_ library. And for those old applications still tied to browsers of the past? You can easily replicate any of the missing native methods with a few lines of code. In fact, I'll provide some simple and intuitive solutions to help you fill in important gaps in ancient browsers.

While jQuery does admittedly save you some keystrokes, you _will_ sacrifice performance. That type of higher-level abstraction is understandably slower than relying directly on native APIs. So what if you want the convenience of jQuery without the sheer size of the dependency and the potential for hidden performance issues? Simple, create your own _very_ thin wrapper around the web API to save you some keystrokes. I'll explore this possibility with some example code before we close out this chapter.


## Core element selectors

In this first section on the topic of finding HTML elements in a document, I'll discuss selecting elements by using some of the more traditional element properties, such as ID, class, and tag name. here, we will compare element selection in jQuery with "vanilla" JavaScript through examples that interface directly with the DOM by making use of the functionality codified in various web API specifications. After completing this section, you will have the necessary confidence and understanding to select elements in the DOM using the most common methods, without relying on jQuery at all.


### IDs {#selecting-ids}

The [W3C HTML4 specification][html4-spec] defines the `id` attribute as one that _must_ be unique among all IDs defined inside of a document. This part of the specification goes on to [describe its primary uses][html4-id], such as element selection and navigation to other sections of a page using anchor links. [The DOM Level 1 specification defines the `HTMLElement` interface][dom1-htmlelement], from which all other elements inherit from. The `id` property is defined in this interface, which is directly connected to the `id` attribute defined on the corresponding element in the markup.

For example, consider the following markup:

{title="HTML element ID - markup", lang=html}
~~~~~~~
<div id="my-element-id"></div>
~~~~~~~

The `<div>` element's `id` attribute is also accessible via the JavaScript representation of the element. This is exposed by the element object's `id` property.

{title="HTML element ID - JavaScript", lang=javascript}
~~~~~~~
// `theDiv` is the <div> from our sample HMTL above
theDiv.id === 'my-element-id'; // returns true
~~~~~~~


#### jQuery

In jQuery-land, obtaining a handle on the `<div>` element object looks something like this:

{title="select by ID - jQuery", lang=javascript}
~~~~~~~
// returns a jQuery object with 1 element -
// the <div> from our sample HMTL above
var result = $('#my-element-id');

// assuming our element has been found in the document
result.is('#my-element-id'); // returns true
~~~~~~~

In the jQuery example, we are using the [ID selector string, which was first defined in the W3C CSS1 specification][css1-id]. The jQuery object returned by this selection attempt is a [pseudo-array](#pseudo-arrays) (which we will discuss in more detail in [the JavaScript utilities chapter](#javascript-arrays)). This psuedo-array contains the `HTMLElement` object representation of this element in the document.


#### Web API

Selecting the same exact element without the help of jQuery is surprisingly easy, and, in fact, the code to achieve this looks surprisingly similar. There are two different ways to select an element by ID using the web API. The first such method involves using the `getElementById` method defined on the `Document` interface, [first formalized in the DOM Level 2 Core specification][dom2core-id]. This method is supported in _all_ browsers in existence (first implemented in Internet Explorer 5.5).

{title="select by ID - web API - all browsers", lang=javascript}
~~~~~~~
// returns the matching HTMLElement - the <div> from our sample
var result = document.getElementById('my-element-id');

// assuming our element has been found in the document
result.id === 'my-element-id'; // returns true
~~~~~~~

{#queryselector-example}
A second approach makes use of the `querySelector` method, which was [first defined on both the `Document` and `Element` interfaces in the W3C Selectors API Level 1 specification][selectors1-queryselector]. Remember that the `HTMLElement` interface, on which the `id` attribute is defined, inherits from the `Element` interface, so `Element`s have an `id` property as well. The `querySelector` method is available in all [modern browsers](#modern-browsers), as well as Internet Explorer 8. In the following example, you will start to notice some stark similarities between the native approach and the jQuery shortcut.

{title="select by ID - web API - modern browsers & Internet Explorer 8", lang=javascript}
~~~~~~~
// returns the matching HTMLElement - the <div> from our sample
var result = document.querySelector('#my-element-id');

// assuming our element has been found in the document
result.id === 'my-element-id'; // returns true
~~~~~~~

I> ## Performance note
I>
I> [`querySelector` is a bit slower than `getElementById`][select-by-id-perf], but this performance gap is closing as browser JavaScript engines evolve.


### Classes {#selecting-classes}

Contrary to the focus of IDs, class attributes do _not_ uniquely identify an element in a document. Instead, classes are traditionally used to semantically group elements in the context of an application as a whole. While IDs can certainly be used to style elements via CSS, this role is most often tied to class attributes. Elements may also be assigned multiple class names, while they are limited to one ID (for obvious reasons). The HTML 4.01 specification goes into more [detail regarding the role of class attributes][html4-classes]. I will discuss working with element attributes in much more detail in [the "understanding HTML attributes" chapter](#element-attributes).

Generally speaking, a valid CSS class is case-insensitive, can only contain alphanumeric characters or hyphen or underscore, and may not start with a digit or two hyphens or a hyphen and a digit. These rules also apply to IDs, along with other element properties used to target elements via CSS. You can read about all of the [allowed values in CSS selectors in the CSS 2.1 specification][css21-selectors].

For example, consider the following markup:

{title="HTML element class - markup", lang=html}
~~~~~~~
<span class="some-class"></span>
~~~~~~~

The `span` element's `class` attribute is also accessible via the JavaScript representation of the element on the object's  `className` property. Notice the inconsistency here - the attribute name is `class` while the corresponding `Element` property is `className`. This is due to the fact that `class` is a reserved word in JavaScript ([even as late as the ECMAScript 5.1 edition specification][es5-future-reserved]), which is why an alternate name exists in the JavaScript representation of an element. For example:

{title="HTML element class - JavaScript", lang=javascript}
~~~~~~~
// `elementObject` is the <span> in our sample markup above
elementObject.className === 'some-class'; // returns true
~~~~~~~


#### jQuery

Selecting an element by class in jQuery looks very similar to the approach used to select an ID. In fact, all element selection in jQuery follows the same pattern.

{title="select by class - jQuery", lang=javascript}
~~~~~~~
// Returns a jQuery object with 0 elements (element not found)
// or all elements with the 'some-class' class attribute.
var result = $('.some-class');

// assuming our element has been found in the document
result.is('.some-class'); // returns true
~~~~~~~

If there happen to be three different elements in the document with a class name of 'some-class', the `result` jQuery object will have 3 entries, one for each match.


#### Web API

As with ids, there are several different way to select elements by class name using the web API. I will demonstrate two of them - both available in [modern browsers](#modern-browsers) as well as Internet Explorer 8 (the last example). The first approach here is the most performant, but the second is clearly the most elegant.

{title="select by class - web API - modern browsers", lang=javascript}
~~~~~~~
// Returns an HTMLCollection containing all matching elements,
// which is empty if there are no matches.
var result = anyElement.getElementsByClassName('some-class');

// assuming our element has been found in the document
result[0].className === 'some-class'; // returns true
~~~~~~~

The first noticeable difference between `getElementById` and `getElementsByClassName` is the fact that the latter returns an [array-like](#pseudo-arrays) object containing _all_ matching elements, instead of a _single_ element. Remember, a document can contain many elements that share the same class name. You may also notice another difference, one which may not be particularly obvious in the simple example provided above. The `getElementsByClassName` method is available on the `Document` interface, just like `getElementById`. However, [it is _also_ defined to be a method on the `Element` interface in the W3C DOM4 specification][dom4-getbyclass]. This means that you may restrict your query to a specific subset of elements when looking for class name matches by specifying an element in the document. When executed on a specific element, only descendant elements are examined for matches. This allows for more focused and efficient DOM traversal.

The `getElementsByClassName` method's return value, a pseudo-array, is defined in the W3C DOM4 specification, and it provides sequentially ordered numeric properties (0, 1, 2, ...), one for each matching element, along with a `length` property and some other methods (of limited usefulness). The most notable attribute of an `HTMLCollection` is the fact that it is a _live_ collection. That is, it is updated automatically to match the underlying elements in the DOM that it represents. For example, if an element contained in the returned `HTMLCollection` is removed from the DOM, it will _also_ be removed from any `HTMLCollection` in scope.

A second approach to selecting elements by class name involves a cousin to the previously demonstrated `querySelector`.

{title="select by class - web API - modern browsers & Internet Explorer 8", lang=javascript}
~~~~~~~
// Returns a NodeList containing all matching elements,
// which is empty if there are no matches.
var result = anyElement.querySelectorAll('.some-class');

// assuming our element has been found in the document
result[0].className === 'some-class'; // returns true
~~~~~~~

Like `getElementsByClassName`, `querySelectorAll` returns all matches in an [array-like](#pseudo-arrays) object. The differences end here, though. For one, `querySelectorAll` returns a `NodeList` object, [an interface which was first formally defined in the W3C DOM Level 3 Core specification][dom3core-nodelist]. `NodeList` differs in one important way from an `HTMLCollection` - it is _not_ a "live" collection. So, if a matching element contained in a `NodeList` is removed from the DOM, it will _not_ be removed from any `NodeList`.

`querySelectorAll` is available both on the `Document` interface _and_ the `Element` interface, [according to the W3C Selectors API][selectors1-queryselectorall] (just like `getElementsByClassName`). The `querySelector` method, which can _also_ be used when looking for an element with a specific class name, will _only_ return the _first_ matching element, which may actually be desirable in some instances. In either case, a CSS selector string must be passed. When looking for a class name, we must include a "." prefix, which was [first described in the CSS 1 specification][css1-classes], although more detail was included in [the later CSS 2.1 specification][css21-classes]. While `getElementsByClassName` is not available in IE8, you can alternatively locate elements by class name in this browser simply by passing a CSS class selector string into the `querySelectorAll` method.

If you _must_ support Internet Explorer 7, or older, the approach for selecting elements by class name can be a bit cumbersome. Since this type of support is rapidly falling out of favor, I have elected to omit the ugly and inefficient legacy solution (which jQuery must rely on anyway). You can have a look at [how a library I maintain solved the issue][fineuploader-getbyclass] back when it supported ancient browsers.


### Element tags {#tag-selector}

The most basic property of any element is its name. In the [IETF HTML specification drafted in part by Tim Berners-Lee way back in 1993][html1], a valid element/tag name may "consist of a letter followed by up to 33 letters, digits, periods, or hyphens." This specification goes on to say "names are not case sensitive". There were also a small number of elements defined in this document, such as the anchor tag (`<a>`), the paragraph tag (`<p>`), and the `<address>` element for supplying contact information. Since this first specification, many more elements have been added, such as [`<video>`][html5-video] and [`<audio>`][html5-audio], added in the relatively recent HTML5 specification.

While custom elements have not been explicitly banned by browsers, there was little motivation to create them, until the [Web Components specification][wc-wiki] came along. Web Components is a collection of specifications, one being [the custom elements specification][custom-elements], which details a way to create new `HTMLElement`s with their own API and properties, or even extensions of existing elements - such as [the ajax-form custom element][ajax-form] which extends and adds features to a native `<form>`.

To setup our examples, let's take the following very simple HTML block into consideration:

{title="HTML tags - markup", lang=html}
~~~~~~~
<code>System.out.println("Hello world!");</code>
~~~~~~~

If you are given an element reference, you can easily determine the element's name via the `tagName` property, which [is defined on the `Element` interface as part of DOM Level 1 Core][dom1core-tagname].

{title="HTML element name - JavaScript", lang=javascript}
~~~~~~~
// `elementObject` is the <code> element from our above HTML
elementObject.tagName === 'code'; // returns true
~~~~~~~


#### jQuery

Selecting elements by jQuery is, predictably, facilitated by passing a CSS element selector into the `$` or `jQuery` function.

{title="select by tag name - jQuery", lang=javascript}
~~~~~~~
// Returns a jQuery object with 0 elements (element not found)
// or all elements with a matching tag name.
var result = $('code');

// assuming our element has been found in the document
result.is('code'); // returns true
~~~~~~~

Nothing magical here. In fact, the [syntax for an element name selector string is defined in the first CSS specification][css1-element]. jQuery has simply provided a simple alias for the native methods available to select elements by tag name, which we will explore next.


#### Web API

Let's start with a quick look at the traditional method for selecting elements by tag name by interfacing directly with the native web API.

{title="select by tag name - web API - all browsers", lang=javascript}
~~~~~~~
// Returns a HTMLCollection containing all matching elements,
// which is empty if there are no matches.
var result = anyElement.getElementsByTagName('code');

// assuming our element has been found in the document
result[0].tagName === 'code'; // returns true
~~~~~~~

The above method has been available as early as DOM Level 1 Core, and, like `getElementsByClassName`, is available on both [the `Document` interface][dom1core-document-tagname] _and_ [the `Element` interface][dom1core-element-tagname]. So, this approach is available on all browsers in existence.

A more "modern" approach involves, as you might expect, `querySelector` or `querySelectorAll`.

{title="select by tag name - web API - modern browsers & Internet Explorer 8", lang=javascript}
~~~~~~~
// Returns a NodeList containing all matching elements,
// which is empty if there are no matches.
var result = anyElement.querySelectorAll('code');

// assuming our element has been found in the document
result[0].tagName === 'code'; // returns true

// -OR-

// ...you can use this if you know there is  only one <code>
// element to find, or if you only care about the first.
// Returns true.
anyElement.querySelector('code').tagName === 'code';
~~~~~~~

{#queryselectorall-slowness}
There is currently [a potentially noticeable performance difference between `getElementsByTagName` and `querySelectorAll(tagName)`][jspef-getelementsbytagname]. The performance consequences of using `querySelectorAll` are apparently attributable to the fact that `getElementsByTagName` returns a live collection of matching elements in the DOM, while `querySelectorAll` returns a static collection. The [latter requires iterating over all elements in the DOM, while the former returns cached matching elements and then queries the document for updates when the list is accessed][nodelist-vs-htmlcollection]. This performance difference is similar to that of `getElementsByClassName` vs `querySelectorAll(classSelector)` for the same reason.


### Pseduo-classes

While the prevalence and number of pseudo-classes has grown substantially in recent versions of the CSS specification, pseudo-classes have existed since [the earliest versions of the CSS specification][css1-pseudoclass]. Pseudo-classes are keywords that add state to a selector string or group of elements. For example, the `:visited` pseduo-class on an anchor selector string will target any links that the user has already visited.  Another example - the `:focus` pseudo-class will target the element that is determined to have focus, such as a text input field that the user is currently interacting with. We will use the latter in our following examples, as [browsers prevent programatic selector access in JavaScript to visited links due to privacy concerns][selectors1-visited].

To setup our examples, let's create a simple form with a couple text inputs, and imagine that the user has clicked on (or tabbed to) the last text input (named "company"). **This last input will be the one that is "focused"**.

{title="simple form field to be used for pseudo-class selection demos", lang=html}
~~~~~~~
<form>
   <label>Full Name
      <input name="full-name">
   </label>
   <label>Company
      <input name="company">
   </label>
</form>
~~~~~~~


#### jQuery

Say we want to select the input that is currently focused, using jQuery.

{title="select the focused input using a pseduo-class selector - jQuery", lang=javascript}
~~~~~~~
// Return value will be a jQuery object containing the
// "company" input element
var focusedInputs = $('INPUT:focus');
~~~~~~~

Above is, once again, a standardized CSS selector string. We are making use of a [tag name selector](#tag-selector) with a pseudo-class modifier. jQuery isn't doing anything special for us at all. In fact, it's simply delegating directly to the web API.


#### Web API

{title="select the focused input using a pseduo-class selector - web API - modern browsers + IE 8", lang=javascript}
~~~~~~~
// Return value will be the "company" text input field element
var companyInput = document.querySelector('INPUT:focus');
~~~~~~~

The above code avoids all of the overhead associated with filtering the call through jQuery. If we were to use jQuery instead (as we have done in the previous example) `querySelectorAll` would have been invoked internally by jQuery's  selector code with the exact same selector string. Since only one element can have focus at once, `querySelector` is more appropriate than `querySelectorAll`. And it's also a bit faster, [for the same reason why any of the `getElementsBy` methods are faster than `querySelectorAll`](#queryselectorall-slowness).


## Selecting elements based on their relations

With some rudimentary approaches to selecting elements fresh in our heads, we're ready to move on to the next step in our trip through element selectors. The following sections will cover selecting elements based on their relation to other elements. We will examine locating children and descendants, parents of children, and elements that are siblings of other elements. The DOM is organized as a tree-like structure. With this in mind, it is often advantageous to be able to navigate this hierarchy of nodes with relations in mind. Just as we already witnessed in the core selectors section, finding elements based on their relations is fairly straightforward _and_ more performant _without_ jQuery.


### Parents and children

Remember from [our discussion on the DOM API](#dom) that an `Element` is a specific type of `Node`. A `Node` or `Element` may have zero _children_ if it is a "leaf" node. Otherwise, it will have one or more immediate children. But every `Node` or `Element` in a document has exactly _one_ immediate parent. Well, almost. There are two exceptions to this rule - one occurs with [the `<html>` tag (`HTMLHtmlElement`)][html5-html] which is the root `Element` in a document, and therefore has no parent `Element` (though it does have a parent `Node`: `document`). This brings us to the second exception, [the `document` object (`Document`)][html5-document], which has neither a parent `Node` _nor_ a parent `Element`. It is _the_ root `Node`.

Let's consider the following simple HMTL fragment:

{title="example markup for parent/children traversal examples", lang=html}
~~~~~~~
<div>
   <a href="http://fineuploader.com">
      <span>Go to Fine Uploader</span>
   </a>
   <p>Some text</p>
   Some other text
</div>
~~~~~~~

In the following code examples, a distinction will be made between targeting child/parent `Node`s and `Element`s. If this distinction is not already clear, first understand that the above HTML fragment is made up of `Element` type object, such as the `<div>`, the `<a>`, the `<span>`, and the `<p>`. Each of these `Element`s are _also_ `Node`s, since the `Element` interface is a subtype of the `Node` interface. But the "Go to Fine Uploader", "Some text", and "Some other text" portions of the above fragment are _not_ `Element`s. But they _are_ `Node`s. More specifically, they are `Text` items. The [`Text` interface][dom3core-text] is a subtype of [the `CharacterData` interface][dom3core-characterdata], which itself implements the `Node` interface.


#### jQuery

jQuery's API includes a `parent` method. To keep things simple, we'll assume that the "current jQuery object" only represents _one_ element. When calling the `parent` method on this object, the resulting jQuery object will contain either the parent `Element`, or, in rare instances, a parent `Node` that is not an `Element`.

{title="get parent element/node - jQuery", lang=javascript}
~~~~~~~
// Assuming $a is a reference to the anchor in our example HTML,
// $result will contain the <div> above it.
var $result = $a.parent();

// Assuming $span is a reference to the <span> in our example HTML,
// the first parent() call references the <a> element, and the
// $result will contain the <div> root element.
var $result = $span.parent().parent();

// Assuming $someText is a reference to the "Some text" Text node,
// the $result will contain the <p> element in our example HTML.
var $result = $someText.parent();
~~~~~~~

To locate children, jQuery provides a `children` method that will return all immediate child `Element`s of a given element. You may also select child elements given a reference element using [the child selector standardized in the CSS 2.1 W3C specification][css2-child]. But since `children` will _only_ return `Element`s, we must use jQuery's `contents` API method to obtain any `Node'`s that are not also `Element`s, such as `Text` items. Again, to keep this simple, we'll assume that the reference jQuery object in our example only refers to _one_ specific element in the DOM.

{title="get child elements and/or child nodes - jQuery", lang=javascript}
~~~~~~~
// Assuming $div is a jQuery object containing the <div> in our example HTML,
// $result will contain 2 elements: <a> and <p>.
var $result = $div.children();

// $result contains the <p> element in the sample markup
var $result = $('DIV > P');

// Again, assuming $div refers to the <div> in our example markup,
// $result will contain 3 nodes: <a>, <p>, and "Some other text".
var $result = $div.contents();

// Assuming $a refers to the <a> element in our example markup,
// $result will contains 1 element: <span>.
var $result = $a.children();

// This returns the exact same elements as the previous example.
var $result = $('A > *')
~~~~~~~


#### Web API {#parents-and-children-webapi}

For the most part, locating the parent of an element/node without jQuery is simple. [DOM Level 2 Core was the first specification to define a `parentNode` property on the `Node` interface][dom2core-parentnode], which, as you might expect, is set to the parent `Node` of the reference element. Of course, this value may be an `Element` or any other type of `Node`. Later on, in [the subsequent W3C DOM4 specification, a `parentElement` property was added to the `Node` interface][dom4-node]. This property will _always_ be an `Element`. If the parent of a reference `Node` is some type of `Node` other than an `Element`, the `parentElement` will be `null`. But in most cases, `parentElement` and `parentNode` will be identical, unless the reference node is `<html>`, in which case `parentNode` will be `document`, and `parentElement` will be of course `null`. In a general sense, and especially due to wide browser support, the `parentNode` property is the best choice, but `parentElement` is nearly just as safe.

{title="get parent element/node - web API", lang=javascript}
~~~~~~~
// Assuming "a" is the <a> element in our HTML example,
// "result" will be the the <div> above it.
var result = a.parentNode;

// Assuming "span" is the <span> element in our HTML example,
// the first parentNode is the <a>, while "result" is the <div>
// at the root of our example markup.
var result = span.parentNode.parentNode;

// Assuming "someText" is the "Some text" Text node in our HTML example,
// "result" will be the the <p> that contains it.
var result = someText.parentNode;
~~~~~~~

{#web-api-children}
There are a number of different ways to locate immediate children of an element using the web API. I will demonstrate two such ways next, and briefly discuss a third approach. The simplest and most common method of locating an element's children in all [modern browsers](#modern-browsers) involves using the `children` property on [the `ParentNode` interface][dom4-parentnode]. `ParentNode` is defined to be implemented by both the `Element` _and_ `Document` interfaces, though it is only commonly implemented on the `Element` interface. It applies to a `Node` that may potentially have children. It was first defined in the [W3C DOM4 specification][dom4], and is only available in [modern browsers](#modern-browsers). `ParentNode.children` returns all children of the reference `Node` in an `HTMLCollection`, which you may remember from earlier in this chapter represents a "live" collection of `Element`s.

{title="get children Elements of a parent - web API - modern browsers", lang=javascript}
~~~~~~~
// Assuming "div" is an Element object containing the <div> in our example HTML,
// result will contain an HTMLCollection holding 2 elements: <a> and <p>.
var result = div.children;
~~~~~~~

A second method used to locate child `Element`s involves using `querySelectorAll` along with [the CSS 2 child selector][css2-child]. This approach allows us to support Internet Explorer 8, in addition to all [modern browsers](#modern-browsers). Remember that `querySelectorAll` returns a `NodeList`, which differs from an `HTMLCollection` in that it is a "static" collection of elements. The collection in this case contains all `Element` children of the parent `Node`.

{title="get children Elements of a parent - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// The result will contain a NodeList holding 2 elements: <a> and <p>
// from our HTML fragment above.
var result = document.querySelectorAll('DIV > *');

// The result will be all <p> children of the <div>, which, in this case
// is only one element: <p>Some text</p>.
var result = document.querySelectorAll('DIV > P');
~~~~~~~

A third option used to select children with the web API involves the [`childNodes` property on the `Node` interface][dom1core-node]. This property was declared on the original [W3C DOM Level 1 Core specification][dom1core]. As a result, it is supported by all browsers, even ancient ones. The `childNodes` property will reveal _all_ child `Node`s, even `Text` and `Comment` items. You _can_ filter out the non-`Element` objects in the collection simply by iterating over the results and ignoring any that have a [`nodeType` property][dom1core-node] that is _not_ equal to `1`. This `nodeType` property was _also_ defined on the original `Node` interface specification.

{title="get children Nodes of a parent - web API - any browser", lang=javascript}
~~~~~~~
// Assuming "div" is an Element object containing the <div> in
// our example HTML, result will contain a NodeList
// holding 3 Nodes: <a>, <p>, and "Some other text".
var result = div.childNodes;
~~~~~~~

Given a parent `Node`, you may also locate either the first and last child, via the aptly named `firstChild` and `lastChild` properties, respectively. Both properties have existed since the original `Node` interface specification, and they refer to child `Node`s, so the first or last child may be a `Text` `Node` _or_ an `HTMLDivElement`, for example. The `firstChild` property can be used as part of a _fourth_ method of obtaining the children of a parent `Node`. [This approach is discussed as part of the sibling element selection section below](#find-children-using-siblings).


### Siblings

DOM `Node`s are siblings if they share the same immediate parent. They may be adjacent siblings (next to each other) or "general" siblings (not necessarily next to each other). There are a number of ways to find and navigate among sibling `Node`s. While I will go over how this is done using jQuery for the purpose of reference, you will see just how easy it is to do this _without_ jQuery as well. The following HTML fragment will be used as a reference point for all demonstration code.

{title="working with siblings - markup for following demos", lang=html}
~~~~~~~
<div id="parent">
   <a href="https://github.com/rnicholus">GitHub</a>
   <span>Span text</span>
   <p>Paragraph text</p>
   <div>Div text</div>
   Text node
</div>
~~~~~~~


#### jQuery

To find all sibling `Element`s of a given `Element`, jQuery provides a `siblings` method as part of its API. For traversing through the siblings of a given `Element`, there are `next` and `prev` methods as well. To keep things simple, I'll simply review how we have all used jQuery to find and traverse through the siblings of a given element.

{title="find and traverse through siblings - jQuery", lang=javascript}
~~~~~~~
// $result will be a jQuery object that contains <a>, <span>, <p>,
// and <div> elements inside of the #parent <div>.
var $result = $('SPAN').siblings();

// $result will be a jQuery object that contains the <a> element
// that precedes the <span>.
var $result = $('SPAN').prev();

// The first next() refers to the <p>, and the 2nd next()
// refers to the <div>Div text</div> element, which is also
// the element contained in the jQuery $result object.
var $result = $('SPAN').next().next();

// The first next() refers to the <p>, and the 2nd next()
// refers to the <div>Div text</div> element. The final next()
// does not reference any element, since the final Node in the
// fragment is a Text Node, and not an element. So, the $result
// is an empty jQuery object.
var $result = $('SPAN').next().next().next();
~~~~~~~

You can also use CSS sibling selectors in jQuery, which we will explore a bit in the web API section next. jQuery actually permits standardized W3C CSS selectors strings for this and other operations.


#### Web API

To mirror the behaviors provided by jQuery's API, I'll cover the following topics associated with sibling traversal and discovery:

1. Locating all siblings of a specific `Element` or `Node`.
2. Navigating through preceding and subsequent siblings of a specific `Element` or `Node`.
3. Locating general and adjacent siblings of an `Element` using CSS selectors.
4. Locating _children_ using the sibling properties on the `Node` interface.

The simplest way to locate all sibling `Element`s of another `Element` is to make use of [the CSS3 general sibling selector][css3-general-sibling]. This approach will work as far back as Internet Explorer 8, and provides you with a `NodeList` of all sibling `Element`s. The W3C CSS2 specification defined [an "adjacent" sibling selector][css21-adjacent-sibling], which only selects the first `Element` matching the selector that occurs after the reference element. Both of the sibling selectors described here are demonstrated in the following code.

{title="find siblings using CSS selectors - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// "result" contains a NodeList of all siblings that occur after the <span>
// in our example HMTL at the start of this section. These siblings are
// the <p> and the <div> elements.
var result = document.querySelectorAll('#parent > SPAN ~ *');

// Another general sibling selector that specifically targets any
// subsequent siblings of the <span> that are <div>s. In our case,
// there is only one such element - <div>Div text</div>. The
// "result" variable is a NodeList containing this one element.
var result = document.querySelectorAll('#parent > SPAN ~ DIV');

// This is an adjacent sibling selector in action. It will target
// the first sibling after the <span>. So, "result", is the same
// as in the previous general sibling selector example.
var result = document.querySelector('#parent > SPAN + *');
~~~~~~~

You'll notice that the general sibling selector (`~`) does _not_ select any elements that precede the reference element - only the ones that follow it.  If you _do_ need to account for any siblings that come before the reference element, you'll need to make use of either the [`Node.previousSibling` property first defined in W3C DOM Level 1 Core][dom1core-node], or the [`previousElementSibling` property, which is part of the `ElementTraversal` interface][elementtraversal-previouselementsibling] first defined in [the W3C Element Traversal specification][elementtraversal]. `ElementTraversal` is an interface that is implemented by any object that also implements the `Element` interface. Simply put, all DOM elements have a `previousElementSibling` property. This is demonstrated in the following code sample.

{title="find both preceding and subsequent siblings of a reference element - web API - modern browsers", lang=javascript}
~~~~~~~
// Find all siblings that follow the <span> in our example HTML
var allSiblings = document.querySelectorAll('#parent SPAN ~ *');

// Converts the allSiblings NodeList into an Array.
allSiblings = [].slice.call(allSiblings);

var currentElement = document.querySelector('#parent > SPAN');

// This loop executes until we run out of previous siblings,
// starting with the sibling before the <span>. Each sibling
// is added to the allSiblings array. After this loop is complete,
// the allSiblings array will contain all siblings of the <span>
// (before and after).
do {
   currentElement = currentElement.previousElementSibling;
   currentElement && allSiblings.push(currentElement);
} while (currentElement);
~~~~~~~

For Internet Explorer 8 support, you will have to use `Node.previousSibling` instead of `Element.previousElementSibling`. This is due to lack of support for the Element Traversal spec in any version of Explorer older than 9. This property returns any `Node`, so you will want to be sure you add a `nodeType` property check if you only want to accept `Element`s.

{title="find both preceding and subsequent siblings of a reference element - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
var generalSiblings = document.querySelectorAll('#parent SPAN ~ *');

var previousSiblings = [],
    currentElement = document.querySelector('#parent SPAN');

do {
   currentElement = currentElement.previousSibling;
   // This differs from the previous example in that we must
   // exclude non-Element Nodes by examining the nodeType property.
   if (currentElement && currentElement.nodeType === 1) {
      previousSiblings.push(currentElement);
   }
} while (currentElement);
~~~~~~~

The web API also exposes a `nextSibling` property on the `Node` interface and a [`nextElementSibling` property on the `ElementTraversal` interface][elementtraversal-nextelementsibling]. Browser support of these properties is identical to their "previous" cousins.

{title="traverse through all subsequent siblings - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// The first nextSibling refers to the <p>, and the 2nd nextSibling
// refers to the <div>Div text</div> element. The final nextSibling
// refers to the "Text node" Text Node, since nextSibling targets
// any type of Node. So, the result is this Text Node.
var result = document.querySelector('SPAN')
                .nextSibling.nextSibling.nextSibling;

// Same as the above example, but the final nextElementSibling returns null,
// since the last Node in the example markup is not an Element. There are only
// 2 Element siblings following the <span>. Note that nextElementSibling
// is not available in ancient browsers.
var result = document.querySelector('SPAN')
                .nextElementSibling.nextElementSibling.nextElementSibling;
~~~~~~~

{#find-children-using-siblings}
In addition to [the methods used to select children outlined in the previous section using the web API](#web-api-children), another such option exists to select _only_ `Element` children of a parent `Node` in _any_ browser. This involves obtaining the `firstChild` of the parent `Node`, locating for the sibling `Node` of this first child, and then continuing to traverse through all sibling elements using the `nextSibling` property on each `Node` until there are no remaining siblings. And finally, to exclude all non-Element siblings (such as `Text` `Node`s), simply check the `nodeType` property of each `Node`, which will have a value of `1` if the `Node` is more specifically an `Element`. This is actually how jQuery implements its `children` method, at least in the late 1.x versions of the library. This implementation choice is likely due to the fact that all of these properties on the `Node` interface have wide browser support, even among [ancient browsers](#ancient-browsers). However, there are much simpler approaches that are supported in modern browsers, so the path just described is really only relevant from an academic or historic perspective.


### Ancestors and descendants

To illustrate the ancestor/descendant `Node` relationship, let's start out with a brief HTML fragment.

{title="ancestors and descendant - markup for examples", lang=html}
~~~~~~~
<body>
   <div>
      <span>random text</span>
         <ul>
            <li>
               <span>item 1</span>
            </li>
            <li>
               <a href="#some-content">item 2</a>
            </li>
         </ul>
   </div>
</body>
~~~~~~~

An element's ancestors are any elements that appear before it in the DOM. That is, its parent, its parent's parent (or grandparent), its parent's parent's parent (great grandparent), and so on. In our above HTML fragment, the anchor element's ancestors include its immediate parent (the `<li>`) along with the `<ul>`, `<div>`, and finally the `<body>` element. Conversely, an element's descendants include its children, its children's children, and so on. In the above markup, the `<ul>` element has four descendants - the two `<li>` elements, the `<span>`, and the `<a>`.


#### jQuery

jQuery's API provides a single method used to retrieve all of an element's ancestors - `parents()`.

{title="retrieve all element ancestors - jQuery", lang=javascript}
~~~~~~~
// Using our HTML example, $result is a jQuery object that
// contains the following elements: <li>, <ul>,
// <div>, and <body>
var $result = $('A').parents();
~~~~~~~

But what if you only want to retrieve the first ancestor that matches a specific condition? In our case, say we are only looking for the first ancestor of the `<a>` that is also a `<div>`. For that, we would use jQuery's `closest()` method. jQuery implements `closest()` by brute-force - through examination of each parent of the reference `Node`.

{title="retrieve closest element ancestor - jQuery", lang=javascript}
~~~~~~~
// Using our HTML example, $result is a jQuery object that
// contains the <div> element.
var $result = $('A').closest('DIV');
~~~~~~~

For locating descendants, you may use jQuery's `find()` method.

{title="retrieve element descendants - jQuery", lang=javascript}
~~~~~~~
// Using our HTML example, $result is a jQuery object that
// contains the following elements: both <li>s, the <span>,
// and the <a>.
var $result = $('UL').find('*');

// $result is a jQuery object that contains the <span>
// under the first <li>.
var $result = $('UL').find('SPAN');
~~~~~~~


#### Web API

The native web does not provide a single API method that returns all ancestors of an element. If this is required in your project, you can accumulate these `Node`s with a simple loop, making use of the [`Node.parentNode` property][dom2core-parentnode] or `Node.parentElement`. Remember that the latter targets only a specific type of `Node`: an `Element`. This is usually what we want anyway, so we will make use of `parentElement` in our examples.

{title="retrieve all element ancestors - web API - any browser", lang=javascript}
~~~~~~~
// When this code is complete, "ancestors" will contain all
// ancestors of the anchor element: <li>, <ul>,
// <div>, and <body>
var currentNode = document.getElementsByTagName('A')[0],
    ancestors = [];

while (currentNode.parentNode) {
   ancestors.push(currentNode.parentElement);
   currentNode = currentNode.parentElement;
}
~~~~~~~

We already know that jQuery provides a method that allows us to easily find the first matching ancestor of an element, `closest`. The web API has a similar method on the `Element` interface, also called `closest`. [`Element.closest()`][dom-element-closest] [is part of the WHATWG DOM "living standard"][whatwg-dom]. This method behaves exactly like jQuery's `closest()`. Unfortunately, browser support for this method is missing from any version of Internet Explorer, Microsoft Edge, and Safari as of 2015. In the following example, I'll demonstrate how the web API's `closest()` method can be used, and I will even include a simple fallback for browsers _without_ native support. Let's again use our example markup and try to locate the closest ancestor of the `<a>` that is a `<div>`.

I> You may remember from the previous chapter that [the WHATWG develops a set of web specifications that differ a bit from the traditional W3C specs](#whatwg-vs-w3c).

{title="retrieve closest element ancestor - web API - Chrome, Opera, and Firefox only", lang=javascript}
~~~~~~~
// "result" is the <div> that exists before the <a>
var result = document.querySelector('A').closest('DIV');
~~~~~~~

{title="retrieve closest element ancestor - web API - all modern browsers", lang=javascript}
~~~~~~~
function closest(referenceEl, closestSelector) {
   // use Element.closest if it is supported
   if (referenceEl.closest) {
      return referenceEl.closest(closestSelector);
   }

   // ...otherwise use brute force (like jQuery)

   // To find a match for our closestSelector, we must use the
   // Element.matches method, which is still vendor-prefixed
   // in some browsers.
   var matches = Element.prototype.matches ||
            Element.prototype.msMatchesSelector ||
            Element.prototype.webkitMatchesSelector,

       currentEl = referenceEl;

   while (currentEl.parentElement) {
      currentEl = currentEl.parentElement;
      if (matches.call(currentEl, closestSelector)) {
         return currentEl;
      }
   }

   return null;
}

// "result" is the <div> that exists before the <a>
var result = closest(document.querySelector('A'), 'DIV');
~~~~~~~

Note that the above cross-browser solution makes use of [`Element.matches`][dom-element-matches], which is also defined by WHATWG in their DOM living spec. This method will return `true` if the element it is called on matches the passed CSS selector. While some browsers, namely IE and Safari, still implement a naming convention consistent with an older version of the specification along with vendor-specific prefixes. I've accounted for these in my example. Most likely, Microsoft Edge's and Safari's implementation of this method will be compliant with the spec in the future.

The above solution may be less elegant, but it makes better use of the browser's native power. jQuery's `closest()` function _always_ uses the most primitive brute-force approach, even if the browser supports `Element.closest` natively. Though in my experience, locating a _specific_ ancestor is an uncommon requirement, making `closest()` a function you can usually live without.

Finding descendants using the web API is just as easy as with jQuery:

{title="retrieve element descendants - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// Using our HTML example, result is a NodeList that
// contains the following elements: the two <li>s, <span>,
// and <a>.
var result = document.querySelectorAll('UL *');

// "result" is a NodeList that contains the <span>
// under the first <li>.
var result = document.querySelectorAll('UL SPAN');
~~~~~~~


## Mastering advanced element selection

What follows are some more advanced methods used to select even more specific elements or groups of elements. While jQuery provides API methods to deal with each scenario, you'll see that modern web specifications _also_ provide the same support, which means that jQuery is not needed in modern browsers for _any_ of these examples. Web API Solutions here will mostly involve the use of various [CSS3 selectors][selectors-level3], which are also usable from jQuery.

All of the native examples in this section are supported in all modern browsers. In some cases, I will also touch on how to achieve the same goals using the web API in ancient browsers as well. Should you find yourself needing some of the following selectors in support of an ancient browser, perhaps you'll forgo pulling in jQuery after understanding how to approach the problem using the browser's native tools instead. Or not, but at least the solution will shed some light on jQuery's inner-workings, which is still beneficial if you insist on making it part of your core toolset.


### Excluding elements {#excluding-elements}

While the ability to exclude specific matches in a set is part of the jQuery API, we'll also see how we can achieve the same results natively with another appropriately named pseudo-class. Before we dive into code, let's consider the following HTML fragment:

{title="excluding elements examples - markup", lang=html}
~~~~~~~
<ul role="menu">
   <li>choice 1</li>
   <li class="active">choice 2</li>
   <li>choice 3</li>
</ul>
~~~~~~~

Imagine this is some sort of menu, with three items to choose from. The second item, "choice 2", is currently selected. What if you want to easily gather all of the menu items that are _not_ selected?


#### jQuery

jQuery's API provides a `not()` method that will remove any elements that match a selector from the original set of elements.

{title="excluding elements - jQuery", lang=javascript}
~~~~~~~
// $result is a jQuery object that contains all
// `<li>`s that are not "active" (the first and last).
var $result = $('UL LI').not('.active');
~~~~~~~

While the above example is idiomatic jQuery, you don't _have_ to use the `not()` function. Instead, you can make use of a CSS3 selector, which we will discuss next.


#### Web API

The native solution for modern browsers is arguably just as elegant as jQuery's, and certainly just as easy. Below, we are using the [W3C CSS3 negation pseudo-class][css3-negation] to locate the non-active list items. There is no library overhead, so this is of course more performant than jQuery's implementation.

{title="excluding elements - web API - modern browsers", lang=javascript}
~~~~~~~
// "result" is a NodeList that contains all
// `<li>`s that are not "active" (the first and last).
var result = document.querySelectorAll('UL LI:not(.active)');
~~~~~~~

But what if we still need to support Internet Explorer 8, which unfortunately does not support the negation pseudo-class selector? Well, the solution isn't as elegant, but still not particularly difficult if we need a quick fix and don't want to pull in a large library.

{title="excluding elements - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
var allItems = document.querySelectorAll('UL LI'),
    result = [];

// "result" will be an Array that contains all
// `<li>`s that are not "active" (the first and last).
for (var i = 0; i < allItems.length; i++) {
   if (allItems[i].className !== 'active') {
      result.push(allItems[i]);
   }
}
~~~~~~~

[The above solution is _still_ more performant than jQuery's implementation of the `not()` method][jsperf-not].


### Multiple selectors {#multiple-selectors}

Suppose you want to select several groups of disparate elements. Consider the following HTML fragment:

{title="multiple selectors markup", lang=html}
~~~~~~~
<div id="link-container">
   <a href="https://github.com/rnicholus">GitHub</a>
</div>
<ol>
   <li>one</li>
   <li>two</li>
</ol>
<span class="my-name">Ray Nicholus</span>
~~~~~~~

What if you want to select the "link-container" _and_ the "my-name" element, along with the ordered list? Let's also assume you want to accomplish this without loops - in one simple line of code.


#### jQuery

jQuery allows you to select multiple unrelated elements simply by providing one long comma-separated string of CSS selectors.

{title="selecting unrelated groups of elements - jQuery", lang=javascript}
~~~~~~~
// $result is a jQuery object that contains 3 elements -
// the <div>, <ol> and the <span> from this section's
// HTML fragment.
var $result = $('#link-container, .my-name, OL');
~~~~~~~


#### Web API

The exact same result can be obtained without jQuery, using the web API. And the solution looks eerily similar to the jQuery solution. It's not that the web API has evolved to match jQuery in this case. Rather, jQuery is, in this case and many others, simply a very thin wrapper around the web API. jQuery fully supports and uses the CSS specification to its advantage. The ability to [select multiple unrelated groups of elements has _always_ been part of the CSS specification][css1-grouping]. Since jQuery supports standard CSS selector strings, the jQuery approach looks nearly identical to the native path.

{title="selecting unrelated groups of elements - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// "result" is a NodeList that contains 3 elements -
// the <div>, <ol> and the <span> from this section's
// HTML fragment.
var result = document.querySelectorAll('#link-container, .my-name, OL');
~~~~~~~


### Element categories and modifiers {#element-categories-and-modifiers}

jQuery's API provides quite a few of their own _proprietary_ CSS pseudo-class selectors, such as `:button`, `:submit`, and `:password`. In fact, jQuery's documentation for these non-standard selectors _advises against using them_, due to the fact that there are much more performant alternatives - _standardized_ CSS selectors. For example, the jQuery API docs for the `:button` pseudo-class contain the following warning:

>Because :button is a jQuery extension and not part of the CSS specification, queries using :button cannot take advantage of the performance boost provided by the native DOM querySelectorAll() method.

I'll demonstrate how to mimic the behavior of a few of jQuery's own pseudo-classes using `querySelectorAll`. These solutions will be far more performant than using the non-standard jQuery selectors. We'll start with `:button`, `:submit`, `:password`, and `:file`.

{title="implementing jQuery's :button pseudo-class - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// "result" will contain a NodeList of all <button> and
// <input type="button"> elements in the document, just like
// jQuery's :button pseudo-class.
var result = document.querySelectorAll('BUTTON, INPUT[type="button"]');
~~~~~~~

{title="implementing jQuery's :submit pseudo-class - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// "result" will contain a NodeList of all <button type="submit"> and
// <input type="submit"> elements in the document, just like
// jQuery's :submit pseudo-class.
var result = document.querySelectorAll(
        'BUTTON[type="submit"], INPUT[type="submit"]'
    );
~~~~~~~

The native solution is a bit more wordy, but not particularly complex, [and certainly more performant that jQuery's `:submit`][jsperf-submit]. You can see the same performance differences [between jQuery's `:button` selector and the native solution][jsperf-button].

{title="implementing jQuery's :password pseudo-class - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// "result" will contain a NodeList of all <input type="password">
// elements in the document, just like jQuery's :password pseudo-class.
var result = document.querySelectorAll('INPUT[type="password"]');
~~~~~~~

{title="implementing jQuery's :file pseudo-class - web API - modern browsers & IE8", lang=javascript}
~~~~~~~
// "result" will contain a NodeList of all <input type="file">
// elements in the document, just as jQuery's :file pseudo-class.
var result = document.querySelectorAll('INPUT[type="file"]');
~~~~~~~

Even [this fairly straightforward native CSS selector is much faster than jQuery's non-standard `:file` pseudo-class][jsperf-file]. Is the performance loss really worth saving a few characters in your code?

jQuery also offers a non-standard `:first` pseudo-class selector. As you might expect, it filters all but the first match in a query's result set. Consider the following markup:

{title="selecting the first match demos - HTML fragment", lang=html}
~~~~~~~
<div>one</div>
<div>two</div>
<div>three</div>
~~~~~~~

Suppose we want to select the first `<div>` in this fragment. With jQuery, our code would look something like this:

{title="selecting the first match - jQuery", lang=javascript}
~~~~~~~
// $result is a jQuery object containing
// the first <div> in our example markup.
var $result = $('DIV:first');

// same as above, but perhaps more idiomatic jQuery.
var $result = $('DIV').first();
~~~~~~~

The native solution is surprisingly simple and [exceptionally performant][jsperf-first] compared to jQuery's primitive implementation:

{title="selecting the first match - web API - modern browsers and IE8", lang=javascript}
~~~~~~~
// result is the first <div> in our example markup.
var result = document.querySelector('DIV');
~~~~~~~

Since `querySelector` returns the first match for a selector string, this is actually a very elegant alternative to jQuery's `:first` pseudo-class or `first()` API method. You'll find many other proprietary CSS selectors in jQuery's arsenal that have straightforward alternatives in the web API.


## A simple replacement for $(selector)

Throughout this chapter, you've seen a number of element selection approaches that involve passing a CSS selector string into the `jQuery` function (aliased as `$`). The native solution often involves the same selector string passed into either `querySelector` or `querySelectorAll`. All things being equal, and assuming we are only using valid CSS selector strings, we can replace the jQuery function with a native solution that is both simple to wire up _and_ more performant than jQuery.

If we focus exclusively on selector support, and only need support for modern browsers, we can get pretty far by forgoing jQuery entirely and replacing it with a surprisingly concise native alternative.

{title="native replacement for jQuery function - all modern browsers, IE8 for CSS2 selectors", lang=javascript}
~~~~~~~
window.$ = function(selector) {
   return document.querySelectorAll(selector);
};

// examples that use our replacement
$('.some-class');
$('#some-id');
$('.some-parent > .some-child');
$('UL LI:not(.active)');
~~~~~~~

The performance benefits seen in some of the more complex selectors exist for the same reason as described earlier when contrasting selecting these same elements using jQuery with the web API. Let's take a look at the child selector in our above code. [Our native solution is definitively faster than jQuery][jsperf-fakejquery], and the syntax is exactly the same between the two. Here, we give up nothing by abandoning jQuery, and gain performance along with a more lean page - a noteworthy theme of this chapter.


[ajax-form]: https://github.com/rnicholus/ajax-form

[css1-classes]: http://www.w3.org/TR/REC-CSS1/#class-as-selector

[css1-element]: http://www.w3.org/TR/REC-CSS1/#basic-concepts

[css1-grouping]: http://www.w3.org/TR/REC-CSS1/#grouping

[css1-id]: http://www.w3.org/TR/REC-CSS1/#id-as-selector

[css1-pseudoclass]: http://www.w3.org/TR/CSS1/#anchor-pseudo-classes

[css21-adjacent-sibling]: http://www.w3.org/TR/CSS21/selector.html#adjacent-selectors

[css21-child]: http://www.w3.org/TR/CSS21/selector.html#child-selectors

[css21-classes]: http://www.w3.org/TR/CSS21/selector.html#class-html

[css21-selectors]: http://www.w3.org/TR/CSS21/syndata.html#characters

[css3-general-sibling]: http://www.w3.org/TR/css3-selectors/#general-sibling-combinators

[css3-negation]: http://www.w3.org/TR/css3-selectors/#negation

[custom-elements]: http://www.w3.org/TR/custom-elements/

[dom-element-closest]: https://dom.spec.whatwg.org/#dom-element-closestselectors

[dom-element-matches]: https://dom.spec.whatwg.org/#dom-element-matchesselectors

[dom1-htmlelement]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-html.html#ID-58190037

[dom1core]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html

[dom1core-document-tagname]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html#i-Document

[dom1core-element-tagname]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html#ID-745549614

[dom1core-node]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html#ID-1950641247

[dom1core-tagname]: http://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html#ID-1950641247

[dom2core-id]: http://www.w3.org/TR/DOM-Level-2-Core/core.html#ID-getElBId

[dom2core-parentnode]: http://www.w3.org/TR/DOM-Level-2-Core/core.html#ID-1060184317

[dom3core-nodelist]: http://www.w3.org/TR/DOM-Level-3-Core/core.html#ID-536297177

[dom3core-characterdata]: http://www.w3.org/TR/DOM-Level-3-Core/core.html#ID-FF21A306

[dom3core-text]: http://www.w3.org/TR/DOM-Level-3-Core/core.html#ID-1312295772

[dom4]: http://www.w3.org/TR/2015/WD-dom-20150428/

[dom4-getbyclass]: http://www.w3.org/TR/2015/WD-dom-20150428/#dom-document-getelementsbyclassname

[dom4-htmlcollection]: http://www.w3.org/TR/2015/WD-dom-20150428/#htmlcollection

[dom4-node]: http://www.w3.org/TR/2015/WD-dom-20150428/#node

[dom4-parentnode]: http://www.w3.org/TR/2015/WD-dom-20150428/#parentnode

[elementtraversal]: http://www.w3.org/TR/ElementTraversal/

[elementtraversal-nextelementsibling]: http://www.w3.org/TR/ElementTraversal/#attribute-nextElementSibling

[elementtraversal-previouselementsibling]: http://www.w3.org/TR/ElementTraversal/#attribute-previousElementSibling

[es5-future-reserved]: http://www.ecma-international.org/ecma-262/5.1/#sec-7.6.1.2

[fineuploader-getbyclass]: https://github.com/FineUploader/fine-uploader/blob/5.2.1/client/js/util.js#L107

[html1]: http://www.w3.org/MarkUp/draft-ietf-iiir-html-01.txt

[html4-classes]: http://www.w3.org/TR/html401/struct/global.html#adef-class

[html4-id]: http://www.w3.org/TR/REC-html40/struct/global.html#adef-id

[html4-spec]: http://www.w3.org/TR/REC-html40/cover.html

[html5-audio]: http://www.w3.org/TR/html5/embedded-content-0.html#the-audio-element

[html5-document]: http://www.w3.org/TR/html5/dom.html#the-document-object

[html5-html]: http://www.w3.org/TR/html5/semantics.html#the-html-element

[html5-video]: http://www.w3.org/TR/html5/embedded-content-0.html#the-video-element

[nodelist-vs-htmlcollection]: http://www.nczonline.net/blog/2010/09/28/why-is-getelementsbytagname-faster-that-queryselectorall/

[jsperf-button]: http://jsperf.com/jquery-button-vs-queryselectorall

[jsperf-fakejquery]: http://jsperf.com/jquery-select-children-vs-native-replacement

[jsperf-file]: http://jsperf.com/jquery-file-vs-queryselectorall

[jsperf-first]: http://jsperf.com/jquery-first-vs-queryselector

[jsperf-getelementsbytagname]: https://jsperf.com/queryselectorall-vs-getelementsbytagname

[jsperf-not]: http://jsperf.com/jquery-not-vs-looping-through-results1

[jsperf-submit]: http://jsperf.com/jquery-submit-vs-queryselectorall

[selectors1-queryselector]: http://www.w3.org/TR/selectors-api/#queryselector

[selectors1-queryselectorall]: http://www.w3.org/TR/2007/WD-selectors-api-20071221/#documentselector

[selectors1-visited]: http://www.w3.org/TR/selectors-api/#privacy

[select-by-id-perf]: http://jsperf.com/getelementbyid-vs-queryselector/11

[selectors-level3]: http://www.w3.org/TR/css3-selectors/

[wc-wiki]: http://www.w3.org/wiki/WebComponents/

[whatwg-dom]: https://dom.spec.whatwg.org/

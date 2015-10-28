# DOM Manipulation

One of the most confusing and misunderstood aspects of the web API pertains to DOM manipulation. No doubt, you are already used to working with DOM elements using jQuery. But is it necessary to continue depending on a library in this regard? In this eight chapter of Beyond jQuery, I'll show you how to create, update, and move elements and element content _without_ any help from third-party code. You'll come to appreciate how easy it is to work with the DOM in virtually all browsers.


## The DOM: A central component of web development

Most likely you've heard _about_ the DOM. It's a common term in the world of web development. But maybe the DOM is still a mostly mysterious word to you. What exactly _is_ the DOM, and why is it so important? How and why does jQuery abstract the DOM API away from developers?

As stated in one of the [early W3C-maintained documents][w3c-dom-article], "The history of the Document Object Model, known as the DOM, is tightly coupled with the beginning of the JavaScript and JScript scripting languages." This model expresses an API, commonly implemented in JavaScript for use in web browsers, that allows programmatic access to an HTML document. In addition to working with [attributes](#element-attributes), [selecting elements](#finding-elements), and [storing data](#html-data), the DOM API also provides a way to create new content, remove elements, _and_ move existing elements around the document. These specific aspects of the DOM API are the primary focus of this chapter. 

### jQuery exists because of the DOM API


### The DOM API is not broken, it's just misunderstood


## Manipulating HTML elements

%% https://developer.mozilla.org/en-US/docs/Web/API/Element
%% https://developer.mozilla.org/en-US/docs/Web/API/Document


### Moving elements around the DOM


#### Reordering sibling elements


#### Inserting elements before and after other elements


#### Adding an element as a child or parent of another


### Making copies of elements


## Creating your own elements and content


### Creating and deleting elements


### Text content

%% textContent, innerText, innerHTML


### Rich content

%% document: outerHTML, innerHTML, write/writeln

[w3c-dom-article]: http://www.w3.org/2002/07/26-dom-article.html

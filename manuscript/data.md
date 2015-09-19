# Associating Data With HTML Elements {#html-data}

Previously, in the [chapter dedicated to attributes](#element-attributes), I [teased data attributes](#data-attributes) when going over the three types of HTML element attributes. Look no further for complete coverage of anything and everything related to data attributes, and connecting data of any kind to your elements. All the details are in _this_ chapter, which will also build on the general attribute and element properties from the previous chapter. JavaScript objects will be introduced briefly as well during a demonstration of connecting non-trivial data structures to document tags.

As you continue through this chapter, expect to understand why connecting data to your document elements is both important _and_ potentially tricky. As always, I'll show you how data is attached to elements and then read back using jQuery initially. But, most importantly, you'll see how to do all of this _without_ jQuery, and how jQuery makes use of the web API and JavaScript to provide its own support for element data. The future of managing element data is exciting. I'll show you how the web API and JavaScript are prepared to eclipse jQuery's support for element data across _all_ browsers in the very near future. And this "futuristic" native support is already available for many browsers, so I'll be sure to include copious code examples detailing how you can make use of this built-in support right now in your projects.


## Why would you want to attach data to elements?

Especially in modern web applications, there is a real need to tie data to the elements on a page. In this section, we will explore common reasons for attaching custom data to your markup, and how this is commonly accomplished at a high level. You will see that there are many reasons why you may find it useful to track data alongside your elements.


### Tracking state

Perhaps you are maintaining a page for a realtor that contains a table of properties up for sale. Presumably, you'd want to order them from most popular to least, and maybe you'd like to be able to adjust this order via drag-and-drop from the page.

{title="table of properties for sale", lang=html}
~~~~~~~
<table>
  <thead>
    <tr>
      <th>Address</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>6911 Mangrove Ln Madison, WI</td>
      <td>$10,000,000</td>
    </tr>
    <tr>
      <td>1313 Mockingbird Ln Mockingbird Heights, CA</td>
      <td>$100,000</td>
    </tr>
  </tbody>
</table>
~~~~~~~

After a row is moved, or perhaps even as part of the initial markup, you may want to annotate each row with its original index in the table. This could potentially be used to revert any changes without calling the server. A `data-` or custom attribute (`data-original-idx` or `original-index` respectively) is most appropriate here.

You may also want to track initial style information for an element, such as dimensions. If you allow user to dynamically adjust the width and height of an element, you will likely want a simple way to reset these dimensions should your user have a change of heart.


### Connecting elements

Seemingly disparate elements may very well need to be aware of each other. For example, two elements whose visibility is determined by the visibility of the other. In other words, if one element is visible, the other is not. How can these elements coexist in this way? Answer: by maintaining references to each other.

This scenario has a number of possible solutions. One would be to embed CSS selectors for each element's partner element inside of a `data-` or custom attribute. Assuming this is reasonable and a unique selector is available, this is probably the best choice. If this is not possible, then you will need to resort to maintaining a map of the `Node` objects. This can be done a couple of different ways with the help of JavaScript objects. More on that later on.


### Storing models directly in your elements

Pretend you have a list of users on a page:

{title="a list of users", lang=html}
~~~~~~~
<ul>
  <li>jack</li>
  <li>jill</li>
  <li>jim</li>
</ul>
~~~~~~~

You may want to associate some common properties with each of these user elements for use by other JavaScript components. For example, the user's age, ID, and email address. This may be best specified as JSON, and _can_ be attached directly to the user element via either a `data-` or custom attribute. You may find that such a solution is not appropriate for non-trivial data, such as JavaScript objects/JSON. In that case, pairing a unique key that identifies the element alongside it's data in a "plain" JavaScript object is more appropriate. You can read more about this approach in the [complete element data storage and retrieval](#complex-data) section below.


## Common pitfalls of pairing data with elements

It' becoming easier to pair data with your elements, thanks to rapidly evolving web and JavaScript specifications. Don't worry, I'll cover specifics very soon. But life is not simple even with these advancements. There is still potential for trouble, provided this new power is not used responsibly. Of course, life _before_ the modern web and JavaScript was much more difficult. Attaching trivial data to elements was done so using primitive means. Storing complex data, such as other `Node`s, could result in memory leaks. I'll cover all of that in this section.

### Memory leaks {#data-memory-leaks}

When connecting two (or more) elements together, the natural instinct is to simply store a reference to the other elements in some common JavaScript object. For example, consider the following markup:

{title="a list of cars - for memory leak example", lang=html}
~~~~~~~
<ul>
  <li>Ford</li>
  <li>Chevy</li>
  <li>Mercedes</li>
</ul>
~~~~~~~

Each of these car types responds to click events, and when one of the cars is clicked, the clicked car must be prominently styled, while all other cars must become less prominent. One way to accomplish this is to store references to all elements in a JavaScript array, and iterate over all elements in the array when one of the list items is clicked. The clicked item must be colored red, while the others should be set to their default color. Our JavaScript might look something like this:

{title="ensure clicked car type stands out - memory leaks example", lang=javascript}
~~~~~~~
var standOutOnClick = function(el) {
    el.onclick = function() {
      for (var i = 0; i < el.typeEls.length; i++) {
        var currentEl = el.typeEls[i];
        if (el === currentEl) {
          currentEl.style.color = 'red';
        }
        else {
          currentEl.style.color = '';
        }
      }
    };
  },
  setupCarTypeEls = function() {
    var carTypes = [],
      carTypeEls = document.getElementsByTagName('LI');

      for (var i = 0; i < carTypeEls.length; i++) {
        var thisCarType = carTypeEls[i];
        thisCarType.typeEls = carTypes;
        carTypes.push(thisCarType);
        standOutOnClick(thisCarType);
      }
  };

setupCarTypeEls();
~~~~~~~

W> ## Don't use inline event handlers
W>
W> In the above example, I am assigning a click handler to an element via the element's `onclick` property. This is known as an inline event handler, and you should avoid doing this. Since I haven't covered events yet, I took this shortcut to keep the code example as simple as possible, but you should _never_ use inline event handler assignments in your code. To learn more about how to properly deal with events without jQuery, take a look at [the upcoming events chapter](#browser-events).

W> ## Avoid inline style assignment
W>
W> In the above example, I change the color of a `<li>` by altering the `color` value of the element's `style` property. While I took this shortcut to keep the example as simple as possible, you should almost always avoid these types of inline style assignments in _your_ code. The proper approach involves removing or adding CSS classes for each element, with the proper styles/colors defined in a stylsheet for these specific classes. For more information on how to properly manipulate element CSS classes without jQuery, see [the class attributes section in the previous chapter](#class-attributes).

The above code works in every browser available, including Internet Explorer 6, though a large hidden issue exists. The above code demonstrates a circular reference involving a DOM object (the JavaScript representation of the `<li>` elements) and a "plain" JavaScript object (the `carTypeEls` array). Each `<li>` references the `carTypeEls` array, which in turn references the `<li>` element. This is a good example of a well documented memory leak present in Internet Explorer 6 and 7. The leak is so severe that the memory may be unclaimed even after a page refresh. Luckily, Microsoft [fixed this issue in Internet Explorer 8][ie8-circular-refs-fix], but this demonstrates some early challenges with storing data alongside HTML elements.


### Managing data

For trivial amounts of data, you can make use of `data-` attributes, or other custom attributes. But what if you need to store a _lot_ of data? You could perhaps attach the data to a custom property on the element. This is known as an ["expando" property](#expando-properties). This is was illustrated in the previous example. To avoid potential memory leaks, you may instead elect to store the data in a JavaScript object along with a selector string for the associated element(s). This ensures the reference to the element is a "weak" one. Unfortunately, neither of these approaches is particularly intuitive, and you get the feeling that you are either reinventing the wheel or writing kludgey, brittle code along the way. Surely there must be an easier way.

Then again, what _is_ a "trivial" amount of data, when do attribute become a less feasible storage and retrieval mechanism? To developers who have not come across this problem before, the large array of approaches may be a bit overwhelming. Can you simply make use of expando properties for all instances? What are the drawbacks and advantages to one approach over the others? Don't worry, you'll not only understand how and when to use a specific approach when storing element data, but you'll also learn how to do it easily and effectively in the final two sections of this chapter.


## Using a solution for all browsers

While there exists some pretty nifty ways to read and track element data in new specifications, such as ECMAScript 6 and HTML 5.1, I realize that the browser support for some of the APIs is not yet comprehensive. In a relatively short amount of time, [these new tools](#data-futures) will be implemented in the vast majority of all browsers in use. But until then, you should understand how to accomplish these same tasks with the most commonly available APIs. In many cases, the approaches described in this section are likely to withstand the test of time and remain most appropriate and simple even as web standards continue to evolve.


### Storing small bits of data using `data-` attributes

Data attributes, which first appeared in [the W3C HTML5 specification][html5-data], in one example of an existing standard that is simple enough to be usable in all current browsers. It's intuitiveness is such that it may be leveraged in future incarnations of web specifications. In fact, data attributes already are a lot more powerful due to a still [narrowly supported Element interface property defined in the HTML5 spec](#element-dataset).

The HTML5 specification declares `data-` attributes to be custom attributes. The two are one in the same. The only valid custom attribute is a `data-` attribute. The specification describes `data-` attributes as follows:

>A custom data attribute is an attribute in no namespace whose name starts with the string "data-", has at least one character after the hyphen

The spec also gives `data-` attributes a specific purpose. They are "intended to store custom data private to the page or application, for which there are no more appropriate attributes or elements." So, if you need to describe a title for an anchor link, use [the `title` attribute][html4-title]. If you must define a language for a paragraph that differs from the language defined on the `<html>` element for the rest of the document, you should use [the `lang` attribute][html4-lang].

But what if you need to store an alternate URL for an `<img>`, one that is used when the image either receives focus via the keyboard or if the user hovers over it using a pointing device, such as a mouse. In this instance, there is no standard attribute to store this information. So, we must make use of a `data-` attribute.

{title="storing an alternate image url", lang=html}
~~~~~~~
<img src="default.png"
  data-zoom-url="default-zoomed.png"
  alt="default image">
~~~~~~~

The image to display on focus/hover is stored in the `data-zoom-url` attribute. We may follow the same approach if we wish to annotate a `<video>` with the offsets at which the scenes change:

{title="storing an alternate image url", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">
~~~~~~~

The above video changes scenes at the 9, 22, and 38 second marks, according to the `data-scene-offsets` custom attribute we've tied to the element.

There are no drastic consequences for defining a custom element that does not confirm to the `data-` convention defined as part of HTML5. The browser will not complain or fail to render your document. But you _will_ lose the ability to utilize any future portions of the API that build on this convention, including the `dataset` property. More on this shortly.


#### Reading and updating `data-` attributes with jQuery

Now that we have a way to annotate our elements with a bit of data via the markup, how can we actually _read_ this data in our code? If you are familiar with jQuery, you probably already know about the `data()` API method. Just in case the details are a little fuzzy, take a look at the following example.

{title="accessing element data - jQuery", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">

<script>
// offsets value will be "9,22,38"
var offsets = $('VIDEO').data('sceneOffsets');
</script>
~~~~~~~

Notice that we must access the value of the `data-` attribute by referencing the unique portion of the attribute name as a camel-case string. Changing the value of the data attribute is very similar:

{title="changing element data - jQuery", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">

<script>
// Does NOT update the attribute. Updates jQuery
// internal data store instead.
$('VIDEO').data('sceneOffsets', '1,2,3');
</script>
~~~~~~~

Notice that there is something peculiar and unexpected about jQuery's `data()` method. When attempting to update the `data-` attribute via this method, nothing appears to happen. That is, the `data-scene-offsets` attribute value remains unchanged in the document. Instead, jQuery stores this value, and all subsequent values, in a JavaScript data store. There are a couple downsides to this implementation:  

1. Our markup is now out-of-sync with the element's data.
2. Any changes we make to the element's data are _only_ accessible to jQuery.

While there are some good reasons for this implementation, it seems unfortunate in this situation.


#### Using the web API to read and update `data-` attributes {#reading-updating-data}

Later, I'll describe [a more modern way to read and update `data-` attributes using JavaScript](#element-dataset) with the same elegance as jQuery's `data()` method but _without_ the drawbacks. In the meantime, let's explore a solution that will work with _any_ browser.

{title="accessing element data - web API - all browsers", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">

<script>
// offsets value will be "9,22,38"
var offsets = document.getElementsByTagName('VIDEO')[0]
  .getAttribute('data-scene-offsets');
</script>
~~~~~~~

We've already seen this before back in the [reading attributes section of the previous chapter](#getattributes). The `data-` attribute is, of course, just an element attribute, so we can easily read it in any browser using `getAttribute`.

As you might expect, updating `data-` attributes without jQuery makes use of the `setAttribute` method of the web API's `Element` interface:

{title="updating element data - web API - all browsers", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">

<script>
// updates the element's data attribute value to "1,2,3"
document.getElementsByTagName('VIDEO')[0]
  .setAttribute('data-scene-offsets', '1,2,3');
</script>
~~~~~~~

This primitive yet effective approach yields two benefits over jQuery's `data()` method in this situation:

1. Our markup is always in-sync with the element's data.
2. Any changes we make to the element's data are accessible to _any_ JavaScript.

So, in this instance, the native solution is arguably the best route.


### Complex element data storage and retrieval {#complex-data}

Simple element data consists of a short string, such as a phrase, word, or short sequence of characters or numbers. Perhaps even a small JSON object or array can be considered simple. But what about complex data? And what _exactly_ is complex data?

Remember the list of cars from the [memory leaks](#data-memory-leaks) section earlier in this chapter? I demonstrated a way to link the individual list item elements such that we could easily highlight the clicked item while making the other items in the last less prominent. In this case, we are associating JavaScript representations of HTML elements with other elements. This can certainly be considered "complex" data.

If we expand upon the previous example with the `<video>` tag, another example of complex element data can be demonstrated. In addition to scene offsets, we also need to record a short description of each scene, along with a title, and a location. What we are describing here is something that must be represented by a proper JavaScript object, instead of a single string of text stored as an attribute value.

The solution I proposed in the memory leaks section involved use of expando properties, which was, in part, responsible for a memory leak in older browsers. Even though this leak has been patched in all modern browsers, expando properties are discouraged, as is modifying JavaScript representations of elements in any non-standard way. The video data scenario I detailed above is far too much data to store in a `data-` attribute. And of course, we shouldn't resort to expando properties here either. So the proper way to associate these types of complex data with elements is to maintain a JavaScript object that is linked to one or more elements via a `data-` attribute. This is the approach jQuery takes, and we can do the same without jQuery fairly easily.


#### The familiar jQuery approach {#html-data-jquery}

The jQuery solution to our problem involves, as you might have already surmised, the `data()` method:

{title="connect scene data to a video element - jQuery", lang=javascript}
~~~~~~~
$('VIDEO').data('scenes', [
  {
    offset: 9,
    title: 'intro',
    description: 'introducing the characters',
    location: 'living room'
  },
  {
    offset: 22,
    title: 'the problem',
    description: 'characters have some issues',
    location: 'the park'
  },
  {
    offset: 38,
    title: 'the resolution',
    description: 'characters resolve their issues',
    location: 'the cemetery'
  }
]);
~~~~~~~

Now, if we want to lookup the title of the second scene:

{title="connect scene data to a video element - jQuery", lang=javascript}
~~~~~~~
// variable will have a value of 'the problem'
var sceneTwoTitle = $('VIDEO').data('scenes')[1].title;
~~~~~~~

jQuery maintains the array we supplied inside of an internal cache object. Each cache object is given an "index", and this index is stored as the value of an expando property jQuery creates on the  `HTMLVideoElement` object, which is the JavaScript representation of the `<video>` element.


#### Using a more natural approach {#html-data-natural}

When deciding how to tie complex data to an element in this section, we must be aware of our three goals:

1. No jQuery.
2. Must work in all browsers.
3. No expando properties.

And we can respect the first two goals by mimicking jQuery's approach to storing element data. To respect the third, we must make some adjustments to jQuery's approach. In other words, we will have to tie out elements to the underlying JavaScript object via a simple `data-` attribute instead of an expando property.

{title="connect scene data to a video element - web API - all browsers", lang=javascript}
~~~~~~~
var cache = [],
  setData = function(el, key, data) {
    var cacheIdx = el.getAttribute('data-cache-idx'),
      cacheEntry = cache[cacheIdx] || {};

    cacheEntry[key] = data;
    if (cacheIdx == null) {
      cacheIdx = cache.push(cacheEntry) - 1;
      el.setAttribute('data-cache-idx', cacheIdx);
    }
  };

setData(document.getElementsByTagName('VIDEO')[0],
  'scenes', [
  {
    offset: 9,
    title: 'intro',
    description: 'introducing the characters',
    location: 'living room'
  },
  {
    offset: 22,
    title: 'the problem',
    description: 'characters have some issues',
    location: 'the park'
  },
  {
    offset: 38,
    title: 'the resolution',
    description: 'characters resolve their issues',
    location: 'the cemetery'
  }
]);
~~~~~~~

What's going on here? First, I've created a convenience method to handle association of data with a specific element - `setData` - along with an array used to hold data for _all_ of my elements - `cache`. The `setData` function has been setup to accept an element, data key, and data object, while the `cache` array holds one JavaScript object per element with data attached to (potentially) multiple key properties.

When handling a call, we first check to see if the element is already tied to data in our `cache`. If it is, we lookup the existing data object in `cache` using the array index stored in the `data-cache-idx` attribute on the element and add a new property to this object that contains the passed data. Otherwise, we create a new object initialized to contain the passed data with the passed key. If this element does not yet have an entry in `cache`, a `data-cache-idx` attribute with the index of the new object in `cache` must be created as well.

As with the jQuery solution, we want to lookup the title of second scene, and we can do that with just a bit more code:

{title="connect scene data to a video element - web API - all browsers", lang=javascript}
~~~~~~~
var cacheIdx = document.getElementsByTagName('VIDEO')[0]
  .getAttribute('data-cache-idx');

// variable will have a value of 'the problem'
var sceneTwoTitle = cache[cacheIdx].scenes[1].title;
~~~~~~~

We could easily create a `getData` function to accompany our `setData` that makes storing and looking up our element data a bit more intuitive. But this all-browser non-jQuery solution is surprisingly simple. For an even more elegant non-jQuery approach that targets more modern browsers, check out the next section, where I will demonstrate the `dataset` element property _and_ the `WeakMap` API.


## The future of element data {#data-futures}

Storing trivial or complex data without jQuery in _all_ browsers is not particularly difficult, but it's also not very elegant. Luckily for us, the web is evolving quickly, and two new APIs exist that should make our code more beautiful, and maybe even a bit more performant. I'll show you how to manage trivial element data with the [HTML5 `dataset` property](#element-dataset), and complex data using the [ECMAScript 6 `WeakMap` API](#es6-weakmap). Keep in mind that everything in this section is meant for the latest browsers only. In each case, nothing older than Internet Explorer 11 is an option. This may seem like an unpleasant restriction in 2015, but in a short amount of time all common browsers will be supported.


### The HTML5 `dataset` property {#element-dataset}

The HTML5 specification, completed in October of 2014, defined a new property on the `HTMLElement` interface - `dataset`. Think of this new property as a JavaScript object available on any element object. In fact, it is an object, more specifically a [`DOMStringMap` object][html5-domstringmap], which is also defined in the HTML5 spec.  Any property you add to the `dataset` object is reflected as `data-` attribute on the element's tag in your document. You can also _read_ any `data-` attribute defined on the element's tag in your document by checking the corresponding attribute's property on the element's `dataset` object. In this respect, `HTMLElement.dataset` provides all of the behaviors you have come to love about jQuery's `data()` method - an intuitive way to read & write data to an element, without the drawbacks. Since changes to the properties on the `dataset` object are always synced to the element's markup, and vice-versa, this new standard property is a perfect way to deal with trivial element data.

`Element.dataset` is currently available on a subset of ["modern" browsers](#modern-browsers) - Internet Explorer 9 and 10 are _not_ supported. Please keep that in mind when viewing the following code examples. And for our first demonstration, let's re-write the first code block first displayed in the [earlier section on reading and updating `data-` attributes using the web API](#reading-updating-data).

{title="accessing element data - web API - modern browsers except IE9 & 10", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">

<script>
// offsets value will be "9,22,38"
var offsets = document.querySelector('VIDEO').dataset.sceneOffsets;
</script>
~~~~~~~

Above, we've simplified the earlier example quite a bit. Notice how we must use the camel-case form of the `data-` attribute. Arguably, the `dataset` model is more intuitive to use than jQuery's `data()` method. We treat all of our data as properties on an object, which is exactly how jQuery represents this data internally. But when using jQuery's API, we have to call a function passing the key as a string argument. Notice how we are also making use of [`querySelector`](#queryselector-example) to grab our `<video>` element. We can do this in any modern browser, as well as Internet Explorer 8.

Let's take a look at a more modern version of the second code example, which illustrates how to change or add data to an element:

{title="updating element data - web API - modern browsers except IE9 & 10", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">

<script>
// updates the element's data attribute value to "1,2,3"
document.querySelector('VIDEO').dataset.sceneOffsets = '1,2,3';
</script>
~~~~~~~

Above, the element data has been updated along with the associated `data-` attribute, all with one simple and elegant line of code. But we can do more! Since `dataset` is a JavaScript object, we can easily remove data from our element, just as we would remove a property from any other JavaScript object:

{title="removing element data - web API - modern browsers except IE9 & 10", lang=html}
~~~~~~~
<video src="my-video.mp4" data-scene-offsets="9,22,38">

<script>
// removes the element's data-scene-offsets attribute
delete document.querySelector('VIDEO').dataset.sceneOffsets;
</script>
~~~~~~~

You can now see how `dataset` actually exceeds the convenience of jQuery's `data()` method.


### Leveraging ES6 `WeakMap` collections {#es6-weakmap}

You already know how to leverage the latest web technology to connect trivial data to elements. But what about complex data? We _could_ make use of our [previous example](#html-data-natural), but maybe the latest and greatest web specifications bring us a more elegant solution, maybe something more intuitive that is a perfect fit for this type of problem.

ECMAScript 6 brings a new collection called [a `WeakMap`][es6-weakmap]. A `WeakMap` can contain keys that are objects, and values that are _anything_ - elements, objects, primitives, etc. In this new collection, keys are "weakly" held. This means that they are eligible for garbage collection by the browser if nothing else references them. This allows us to safely use the reference elements as keys!

While WeakMap is only supported in the latest and greatest browsers (Inernet Explorer 11+, Chrome 36+, Safari 7.1+) along with Firefox 6+, it provides an exceptionally simple way to associated HTML elements with data. Remember the [all-browser code examples](#html-data-natural) demonstrated earlier? Let's rewrite it using `WeakMap`:

{title="connect scene data to a video element - web API - modern browsers except IE9 & 10", lang=javascript}
~~~~~~~
var cache = new WeakMap();
cache.set(document.querySelector('VIDEO'), {scenes: [
  {
    offset: 9,
    title: 'intro',
    description: 'introducing the characters',
    location: 'living room'
  },
  {
    offset: 22,
    title: 'the problem',
    description: 'characters have some issues',
    location: 'the park'
  },
  {
    offset: 38,
    title: 'the resolution',
    description: 'characters resolve their issues',
    location: 'the cemetery'
  }
]});
~~~~~~~

Thanks to `WeakMap`, we've managed to eliminate _all_ of the boilerplate from our earlier non-jQuery example. The elegancy of this approach equals that of jQuery's `data()` method, which I also [demonstrated earlier](#html-data-jquery).  Looking up data is just as easy:

{title="connect scene data to a video element - web API - modern browsers except IE9 & 10", lang=javascript}
~~~~~~~
// variable will have a value of 'the problem'
var sceneTwoTitle = cache.get(document.querySelector('VIDEO')).scenes[1].title;
~~~~~~~

And finally, we can clean up after ourselves by removing elements we no longer want to track with a simple API call:

{title="removing element data - web API - modern browsers except IE9 & 10", lang=html}
~~~~~~~
cache.delete(document.querySelector('VIDEO'));
~~~~~~~

The web without jQuery is looking pretty powerful.

[es6-weakmap]: http://www.ecma-international.org/ecma-262/6.0/#sec-weakmap-objects

[html4-lang]: http://www.w3.org/TR/html4/struct/dirlang.html#adef-lang

[html4-title]: http://www.w3.org/TR/html4/struct/global.html#edef-TITLE

[html5-data]: http://www.w3.org/TR/html5/dom.html#embedding-custom-non-visible-data-with-the-data-*-attributes

[html5-dataset]: http://www.w3.org/TR/html5/dom.html#dom-dataset

[html5-domstringmap]: http://www.w3.org/TR/html5/infrastructure.html#domstringmap-0

[ie8-circular-refs-fix]: https://msdn.microsoft.com/en-us/library/dd361842(VS.85).aspx

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

### Memory leaks

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

For trivial amounts of data, you can make use of `data-` attributes, or other custom attributes. But what if you need to store a _lot_ of data? You could perhaps attach the data to a custom property on the element. This is known as an ["expando" property][(#expando-properties)]. This is was illustrated in the previous example. To avoid potential memory leaks, you may instead elect to store the data in a JavaScript object along with a selector string for the associated element(s). This ensures the reference to the element is a "weak" one. Unfortunately, neither of these approaches is particularly intuitive, and you get the feeling that you are either reinventing the wheel or writing kludgey, brittle code along the way. Surely there must be an easier way.

Then again, what _is_ a "trivial" amount of data, when do attribute become a less feasible storage and retrieval mechanism? To developers who have not come across this problem before, the large array of approaches may be a bit overwhelming. Can you simply make use of expando properties for all instances? What are the drawbacks and advantages to one approach over the others? Don't worry, you'll not only understand how and when to use a specific approach when storing element data, but you'll also learn how to do it easily and effectively in the final two sections of this chapter.


## Using a solution for all browsers

### Storing small bits of data using `data-` attributes

#### Reading and updating `data-` attributes with jQuery

#### Using the web API to read and update `data-` attributes

### Complex element data storage and retrieval {#complex-data}

### The familiar jQuery approach

### Using a more natural approach


## The future of element data

### The HTML5 `dataset` property

### Leveraging ES6 `WeakMap` collections


[ie8-circular-refs-fix]: https://msdn.microsoft.com/en-us/library/dd361842(VS.85).aspx

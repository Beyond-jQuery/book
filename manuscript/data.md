# Associating Data With HTML Elements {#html-data}

Previously, in the [chapter dedicated to attributes](#element-attributes), I [teased data attributes](#data-attributes) when going over the three types of HTML element attributes. Look no further for complete coverage of anything and everything related to data attributes, and connecting data of any kind to your elements. All the details are in _this_ chapter, which will also build on the general attribute and element properties from the previous chapter. JavaScript objects will be introduced briefly as well during a demonstration of connecting non-trivial data structures to document tags.

As you continue through this chapter, expect to understand why connecting data to your document elements is both important _and_ potentially tricky. As always, I'll show you how data is attached to elements and then read back using jQuery initially. But, most importantly, you'll see how to do all of this _without_ jQuery, and how jQuery makes use of the web API and JavaScript to provide its own support for element data. The future of managing element data is exciting. I'll show you how the web API and JavaScript are prepared to eclipse jQuery's support for element data across _all_ browsers in the very near future. And this "futuristic" native support is already available for many browsers, so I'll be sure to include copious code examples detailing how you can make use of this built-in support right now in your projects.


## Why would you want to attach data to elements?

Especially in modern web applications, there is a real need to tie data to the elements on a page. In this section, we will explore common reasons for attaching custom data to your markup, and how this is commonly accomplished at a high level. You will see that there are at least four reasons why you may find it useful to track data alongside your elements.


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

After a row is moved, or perhaps even as part of the initial markup, you may want to annotate each row with its original index in the table. This could potentially be used to revert any changes without calling the server. A `data-` or custom attribute, `data-original-idx` or `original-index` respectively, is most appropriate here.


### Connecting elements

### The need for proprietary attributes

### Connecting data models directly to elements

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

### Memory leaks

### Managing the data

### Noisy markup


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

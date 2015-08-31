# Associating Data With HTML Elements {#html-data}

Previously, in the [chapter dedicated to attributes](#element-attributes), I [teased data attributes](#data-attributes) when going over the three types of HTML element attributes. Look no further for complete coverage of anything and everything related to data attributes, and connecting data of any kind to your elements. All the details are in _this_ chapter, which will also build on the general attribute and element properties from the previous chapter. JavaScript objects will be introduced briefly as well during a demonstration of connecting non-trivial data structures to document tags.

As you continue through this chapter, expect to understand why connecting data to your document elements is both important _and_ potentially tricky. As always, I'll show you how data is attached to elements and then read back using jQuery initially. But, most importantly, you'll see how to do all of this _without_ jQuery, and how jQuery makes use of the web API and JavaScript to provide its own support for element data. The future of managing element data is exciting. I'll show you how the web API and JavaScript are prepared to eclipse jQuery's support for element data across _all_ browsers in the very near future. And this "futuristic" native support is already available for many browsers, so I'll be sure to include copious code examples detailing how you can make use of this built-in support right now in your projects. 


## Why would you want to attach data to elements?

Especially in modern web applications, there is a real need to tie data to the elements on a page.

### Tracking state

### Connecting elements

### The need for proprietary attributes

### Connecting data models directly to elements


## Common pitfalls of pairing data with elements

### Memory leaks

### Managing the data

### Noisy markup


## Using a solution for all browsers

### Storing small bits of data using `data-` attributes

#### Reading and updating `data-` attributes with jQuery

#### Using the web API to read and update `data-` attributes

### Complex element data storage and retrieval

### The familiar jQuery approach

### Using a more natural approach


## The future of element data

### The HTML5 `dataset` property

### Leveraging ES6 `WeakMap` collections

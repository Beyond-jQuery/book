# You Don't _Need_ jQuery (anymore)

The primary goal of this chapter is to explain why a web developer, like yourself, _should_ or _should not_ use jQuery when developing a library or application. For example, file size is one common consideration when choosing browser-based dependencies. And I will cover the importance of this attribute and determine how it factors into your decision to utilize this library. Including exploration of the file-size argument, the usefulness of jQuery will be further analyzed. As part of this particular exploration effort, I will contrast the common reasons why developers choose to use jQuery against the problems that the same developers may run into as a result of this choice. I may even briefly investigate and discuss other libraries that may be used in place of jQuery or even propel it into obsolescence, though this will mostly be limited. The focus of third-party code will be centered around adopting smaller and more focused libraries, and shims. The future of the native functionality provided by the browser will also be a point of discussion.

Upon completion of this chapter, you will in a better position to decide if jQuery should be part of your current or future projects. Your understanding of the importance of such a library will become clear, and many common meritless excuses will be refuted. You will also be empowered with choices. If you do desire a bit of help on a complex project with ambitious goals, jQuery is never your only option. The future of web development, in terms of the evolving native browser tools, will give you confidence if you _do_ decide to leave jQuery behind. The term "anymore" in the title of this chapter has a double meaning. You don't need jQuery anymore because the web API and JavaScript are sufficiently evolved such that a wrapper library can be omitted in favor of a closer-to-the-metal approach. You don't need jQuery anymore due to the fact that your confidence and knowledge as a web developer will _also_ be sufficiently evolved after reading this book.


## Need vs. want

The struggle between _need_ and _want_ is not specific to software development, but it is a particularly prudent conflict to be aware of when planning a web project. Often when we make decisions regarding dependencies, IDEs, and build tools, our choices are more focused on want instead of need. Why do some of us choose WebStorm over vim? Certainly vim provides us with everything we need to develop a full-stack web application, but we may feel more comfortable with WebStorm due to its flashy UI and exceptional usability and intuitiveness. Why not use Make or shell scripts instead of grunt or gulp? We can define tasks to automate aspects of our project's build system with a makefile, but grunt provides a more intuitive set of conventions and integrations that JavaScript developers can easily grasp.

What we _need_ is often trumped by what we _want_. New developers are often more motivated to produce observable progress, above all else, in each and every project, each and every time. Emerging coders aim to prove themselves and use any help they can get from their tools in their pursuit of recognition and self-confidence. I know this to be true, as I was a new developer once myself, and observed the same qualities of many of my peers. As a more experienced developer, I now have a much more minimalistic approach to my toolset. I see this same state of mind in _some_ others, but more seem to continue to center on churning out code and features above all else.

Some get satisfaction from masterful understanding and application. But most seem to be more interested in adopting new bleeding-edge high-level tools that promise to take them further than the traditional set ever could.  Closer-to-the-metal solutions are seen as primitive, weak, and unnecessarily complex. Their years of existence are glossed over in favor of an abstraction that vows to be more powerful than the old tool, but easier to use. Maintaining a modest set of tools is admirable to some, but generally not a goal.

Pulling jQuery into a project is often the result of a _want_ or an _unfounded need_. Its magical reputation is due more to lore than an objective analyzation of need vs want.  The truth is, it isn't magic. jQuery, while potentially elegant and helpful, is nothing more than a wrapper around the web API and an extension of JavaScript. It's an abstraction, a simplification, and a mechanism for convenience. But make no mistake about it, the real power comes from the underlying language and the tools native to the browser. While jQuery can indeed be helpful in some ways, we never literally _need_ jQuery. Of course, the same is true of many other abstractions. And while the title of this chapter may suggest otherwise, the goal here is not to quibble about semantics.


## Both sides of the acceptable use argument

The goal of this book is not to declare jQuery to be "persona non grata". My intention is _not_ to pick on jQuery, but rather to teach about the browser's native tools and provide you with the confidence to develop your web projects without feeling helplessly dependent on a library. So, let's discuss frankly when it is acceptable to put the "magic" of jQuery to use in your project, and when it is not. Let's put necessity aside for a bit, and focus more on _want_. With a proper understanding of the instances in which jQuery is an acceptable choice, you will be able to make the correct decision when planning future projects.


### When is it acceptable to use it?

If you are quite comfortable with front-end web development and are just looking to write more elegant code, admittedly there aren't many good reasons to avoid jQuery as a project dependency. This doesn't mean that you absolutely _should_ use it, but feel free to do so if you desire. If you also feel comfortable with jQuery, and you are familiar enough with how jQuery works its magic, then, by all means, keep using it.

There are certain aspects of "ancient" browsers that may make jQuery, or at least certain modules of the library, worthwhile. Let's define an "ancient" browser as one that is older than Internet Explorer 9. Anything that isn't an "ancient" browser can be considered a "modern" browser. We'll talk more about ancient, modern, and evergreen browsers in [the next chapter](#understanding-the-web).

Ancient browsers tend to have substantial API differences, compared to modern browsers. Take event handling as an example. In Internet Explorer 8 and older, event handlers must be registered with the `attachEvent` method and event names passed to `attachEvent` must be prefixed with "on". Also, some important properties and methods of the Event object are non-standard. Input element "change" events do not bubble, and event capturing is not supported at all.

These browsers also leave a lot to be desired in terms of API and feature support. Ancient browsers lack CSS3 selector support. The useful `indexOf` property is missing on the `Array` prototype. Very old browsers cannot natively parse or create JSON and lack a way to easily distinguish an element from an object. These are just a few of the struggles ancient browsers present. In _some_ cases, jQuery was especially important when these browsers were commonly supported. If you are in the unusual and unfortunate position to require support for such an old browser, jQuery _may_ not be a bad library to import.

There is usually little benefit to extracting jQuery from a large legacy project. In cases where an enterprise web application has shed support for ancient browsers, it may be tempting to attempt to eradicate unnecessary browser-based dependencies. I have found myself in this very situation more than once. From my experience, large multi-purpose all-encompassing libraries such as this tend to become firmly entrenched in a complex project over time. Perhaps a planned major rewrite of the application is a prudent excuse to remove these types of monolithic dependencies, but anything short of this would likely render such an undertaking fruitless. Unless your suite of front-end automated tests are exceptionally comprehensive, you may find that the risk of removal far outweighs any perceived drawbacks to leaving the library in place.

When writing unit tests for your front-end code, and you should always write tests, jQuery is an acceptable dependency. In a testing environment, performance and page load time are not notable factors, nor is file size. In fact, writing unit tests using some higher-level language or abstraction has some observable benefits. A common thought is that unit tests should not only serve to test your code, but to document it in terms of expected behaviors. An elegant and terse testing framework certainly makes tests more maintainable and, most importantly, readable.

Finally, there is no shame in using a little help on a one-off project. Someone without any career ambitions in the web development space working on a small and straightforward project is probably not giving up anything by leaning on jQuery to expedite the process. If you're simply not a developer and need to get, for example, a Wordpress site up and running, jQuery can be a notable asset. This is a situation where the car and driver analogy holds up. In this case, you are a driver, not a mechanic. The browser is a merely convenience and not a central tool of your trade.


### When should you refrain from using it?

If your project only supports modern browsers, especially [evergreen browsers](#evergreen-browsers), you may find it especially easy to do without the conveniences that a wrapper library provides. As browsers evolve, so does the web API and JavaScript. The higher-level amenities that a library like jQuery provide are quickly being represented natively in modern browsers as the associated specifications evolve. For example, the ability to add, remove, and check for CSS classes was previously a chore without jQuery's `addClass()`, `removeClass()`, and `hasClass()` methods. But the web specification caught up and now provides a native `classList` property on every element with `add()`, `remove()`, and `contains()` methods. This is perhaps an example of jQuery's powerful influence on the web specification – no argument there. As the browser's native API continues to push forward, the necessity of jQuery also decreases. Instead of pulling in a redundant dependency in new projects, consider relying on the power of the browser instead.

When writing a generic re-usable library, especially an open-source once, your instinct should be to keep third-party dependencies to a bare minimum. Your library's dependencies become your user's dependencies as well. Due to the current ubiquitousness of jQuery, you might think it safe to use in any exported code. Odds are, projects making use of your library are already using jQuery. But, what if they aren't? Will a discerning web developer pull in a large transitive client-side dependency solely for the use your library? Perhaps not. This scenario will become much more common as the web evolves and developers elect to shed these types of abstractions. Personally, _I_ would skip over a library with otherwise unnecessary functional dependencies, and I don't believe that this position is unique, based on input I have received from users of a large JavaScript library I maintain. As a library developer, your job is to solve complex problems and package them up in a box that is proportional to the size and scope of the problem you are solving.

Performance of your application is perhaps another reason to reject high-level dependencies, especially one as complex as jQuery. As the _user_ of such a mature and popular library, you would naturally assume that the most basic and common areas of the codebase are all heavily optimized. In this context, efficiency is expected. Sure, perhaps some of the more complex and lesser-used functions have some performance implications. But all of the basic convenience methods should be quite performant. Unfortunately, in the case of jQuery, this is not always true.

Take the `hide()` method as an example of the potential performance issues that lay beneath the surface. This seems like a simple operation to implement efficiently. In fact, it _is_ relatively simple to do this. One approach involves defining a proprietary CSS class name in the document tied to a style of `display: none`. Instead of using a CSS class, perhaps a hidden attribute can be tied to this style instead. On `hide()`, add the class or attribute to the element.  On `show()`, remove it. This results in a simple and performant solution to a simple problem. However, jQuery's solution to this simple problem is quite complex and inefficient.

One major performance bottleneck in jQuery's implementation of the `hide()` method is due to the use of `getComputedStyle()` which is a Web API method that computes the actual set of styles of an element taking into account CSS files, `<style>` elements, and inline or JavaScript modifications to the element's `style` property. The use of `getComputedStyle()` is appropriate in some cases, but hiding an element doesn't seem to be one of them. The use of this method in jQuery's `hide()` implementation has _serious_ performance implications. Benchmark testing indicates that [this approach is about _90 times slower_ than simply defining a style via an attribute and setting that attribute on the element to be hidden][jsperfhide][^jsperfchrome41]. This specific performance issue is likely to be an unexpected one, even to a seasoned developer. There are other similar issues in jQuery revolving around the use of its effects and CSS support, which will be covered in more detail in the [styling elements chapter](#styling-elements), as well as the [chapter on effects](#effects-and-animations).

If you want to maintain ultimate control over the performance of your code, you should think twice before pulling in this type of library, lest you unexpectedly run across other efficiency bottlenecks. Of course some performance issues may be more related to your use of the library than anything else. But still, it is surprisingly simple to unknowingly write inefficient code with jQuery. Consider the following code that loops over a set of elements that contain a CSS class of "red" and removes any that include an attribute of "foo" with a value of "bar":

{title="Removing elements with jQuery - naïve approach", lang=javascript}
~~~~~~~
$('.red').each(function() {
   if($(this).attr('foo') === 'bar') {
      $(this).remove();
   }
});
~~~~~~~

The above code certainly works, but it has some notable performance issues. A novice developer and jQuery user without a good understanding of CSS selectors and the implications of looping over a large set of elements, may not know that there is a much simpler and more efficient way to solve the same problem. Here is a much more performant and elegant solution to the same problem:

{title="More performant approach to removing elements with jQuery", lang=javascript}
~~~~~~~
$('.red[foo="bar"]').remove();
~~~~~~~

The execution time between the two approaches is not noticeably different given a small document. But if the document domains a large number of elements with a CSS class of "red", say, 200, the consequences of the first approach are notable. The former solution is [about six times slower than the one-line solution with a complex CSS selector][jsperfjqueryselector][^jsperfchrome42].

You _can_ still write highly performant code with jQuery, provided you know which methods in the API to avoid, and when. The same is true of the browser's API, but libraries often provide a false sense of security. Code that makes use of the web API directly is a bit more explicit and specific in its intentions. jQuery, on the other hand, provides a very high-level and seemingly magical API that obscures many details of the implementation and glosses over potential performance tradeoffs. We often don't want to look past the abstraction's conveniences, but you must. If you want to write solid and highly efficient code, you must not only understand jQuery (if you choose to use it) but how jQuery makes use of the web API. Blind faith here can present problems in many forms.

Another consideration that may require avoidance of jQuery involves page load time. An over-reliance of jQuery's `ready()` method is one example. The `ready()` method executes a passed function only after all elements in the document have been loaded on the page. This doesn't really have a notable effect on _actual_ page load time, but it does effect _perceived_ page load time. Commonly, any code to be executed by jQuery's `ready()` method is imported at the top of the document (usually in the `<head>` element). And, if all scripts are loaded at the top of the document, this may result in a noticeable delay in page rendering, since the scripts must be loaded and executed before the elements. The recommended approach, wherever possible, is to load all scripts at the _bottom_ of the document instead. This results in a much faster perceived page load time as the document elements load before anything else. If you follow this convention, using `$.ready()` is no longer necessary. The use of jQuery's `ready()` method is widespread and even used regularly in the example code on jQuery's learning site. This is another instance where you would be better off understanding all the possible options (such as loading your scripts at the bottom of the page) instead of blindly relying on the convenience methods that jQuery provides, such as `ready()`.

Somewhat connected to page load time is file size. When talking about "file size" we are referring to the size, in bytes, of any resources that your page must load in order to fully render on page load. One common argument against depending on libraries, such as jQuery, revolves around file size. The reality is that bandwidth is limited, and all client-side dependencies downloaded by the browser on page load consume some of this bandwidth. If your users all have a 60 Mbps downstream pipe available, the scripts pulled down by your application will probably not have any noticeable effect on page load times. But what if your users are not so lucky? What if they only have access to DSL, which maxes out at 6 Mbps downstream? What if you are targeting mobile devices? In that case, downstream bandwidth may not exceed 4 Mbps. In developing nations, your users may only have access to EDGE, which peaks at about 400 Kbps. Are you considering _all_ of your users?

The size of jQuery may or may not be significant to you and your users. If you decide to load jQuery from a CDN, there is a much greater chance that the round trip will be avoided altogether. Because this library is so popular, there is an excellent chance that many of your users have already cached jQuery in their browser from another application. But this is certainly not guaranteed. There are also potential disadvantages to depending on a third-party server for your production runtime dependencies. If this server experiences technical issues, your application is likely crippled as a result, even if all servers under your control are functioning as expected. Depending on a public CDN can also negatively impact users that are far outside the range of the CDN's server, resulting in a longer time to load dependencies that are not already cached.

If you host jQuery yourself or via a private CDN, you have much more control over how it is served, and where it is served from (taking the user's location into account). Or perhaps you are worried about the overhead of individual HTTP requests, and elect to serve jQuery combined with all other page resources as the response to a single request. Combined with GZIP compression, this is not a bad strategy. But when your user base relies on exceptionally low-bandwidth connections, keeping your resource list small is still of utmost importance. If the first page load takes a noticeable amount of time, you may just lose a potential customer.

To be fair, I should mention that jQuery 1.8 exposed a build task in their project source that allows jQuery to be "custom" built, excluding any modules that that may not be needed for a specific project. This perhaps negates the file size argument. But a question remains: Will new and inexperienced developers really know what parts of jQuery they need? Do most developers even know the ability to create a custom build of jQuery exists? The answer to both questions is, most likely, "no". Unfortunately, the ability to create a custom build is hidden inside of a build file in jQuery's source tree. In order to make use of it, you must pull down the entire repository, install the development dependencies required to build jQuery, search through the build file, and run the task using their grunt build tool with any undesired modules excluded. With all of these steps, it is unlikely that most jQuery-dependent developers will use anything other than the full build files available on the user-facing download page.

All of the above points bring to light the compromises made when developing popular monolithic libraries. There is no disputing that a great deal of care and detail has gone into development of jQuery since its inception. But it may not have _you_ in mind or _your_ edge cases, or even _your_ goals. Do you want both convenience _and_ speed? These two goals may be at odds with each other, depending on your workflow and intended use of the library. Do you desire seamless file uploading, or a minimal footprint? Neither of these are jQuery goals either. jQuery, like any other large library with a huge user base, has to take great care to focus mostly on the greatest common divisor in terms of features and workflow.


## Should you use other libraries instead?

One goal of this book is to push you to remove your dependence on jQuery. But the only alternative I have offered to a monolithic wrapper is a direct attachment to the browser's native API. This is certainly an admirable goal, and in a sense, the browser is all we need to develop all of our front-end projects. But realistically, we may need a bit more help to reduce the inevitable hand-wringing that occurs when putting together something overly complex. The assumption is that we are targeting modern browsers, and this is a reasonable one given the current state of the web. But even "modern" browsers with an evolved API may have inconsistent support for some of the powerful features that we need for our projects. If only there was some way to make consistent use of modern web and JavaScript features across all modern browsers, without wrapping the entire stack...


### Small shims over large wrappers

There is a concept in web development that describes a very specific type of library, a "regressive library". This is a new term, one you have probably never heard of before, because I just coined the term myself. Regressive libraries are a reasonable alternative to large wrapper libraries. While they are _usually_ small (though not always), their true appeal is evident in the name – they are _regressive_. While most libraries evolve in size and feature sets over time, regressive libraries devolve. The ultimate goal of a regressive library is to disappear, to be replaced entirely by the browser's native API.

Regressive libraries are more popularly known as "shims" or "polyfills". They don't provide _any_ new APIs – their job is to temporarily fill in missing implementations for standardized APIs in non-compliant browsers. These libraries keep us focused on native tools. There are no abstractions to cloud our understanding and hide the true nature of the web. Polyfill code is usually constructed such that it is only ever used if the browser _does not_ contain a matching native implementation. If the browser _does_ contain appropriate native code, the library delegates directly to the browser instead.

One example of a commonly used polyfill is [the json2 library][json2] by Douglas Crockford. It contributes an implementation for the `JSON` object in browsers that do not contain their own native implementation. As expected, the API of json2.js is a one-to-one match to the JSON API standardized in the [ECMAScript 5 specification][es5]. The specification describes a JavaScript object that contains methods for turning a JSON string into a JavaScript object, and a JavaScript object back into a JSON string again. These methods are quite useful and important when serializing and deserializing data as part of communication with a JSON aware endpoint. Json2.js ensures that this API is available in older browsers that do not implement this particular ECMAScript 5 specification (such as Internet Explorer 7).

There are quite a few other popular shims such as babel, webcomponents.js, and fetch. Their names give a good indication as to the native APIs they are responsible for patching. Currently, all three of these polyfills contribute implementations for bleeding edge specifications. Babel is responsible for the new JavaScript functions, objects, and syntax defined in the ECMAScript 2015 standard. Webcomponents.js contributes patches for browsers that do not completely implement the Web Components browser specification (all browsers other than Chrome at the moment). And fetch allows developers to make use of [the new WHATWG-created `fetch` specification][fetch], an eventual replacement for `XMLHttpRequest`. Most of these concepts and specifications will be explored later on in this book, especially in [the next chapter](#understanding-the-web).


### Writing your own shim

When we want to use some new and exciting cutting-edge features of the web API and JavaScript in our projects _and_ maintain support for a wide range of browsers _and_ ensure that the footprint of our dependencies is as small as possible and temporary, we turn to regressive libraries. But what exactly does a polyfill look like? How does one go about creating such a library? These questions are more pragmatic than academic when you find yourself needing to use a common and useful native API method in an older browser without an available pre-existing polyfill at your disposal. Creating your own polyfill _may_ not be as complicated as you might expect. Don't believe me? Let's create one, right now!

Take the `find()` method, available in JavaScript `Array`s, which is part of the ECMAScript 2015 specification. `Array.find` returns an entry in an array that satisfies a provided condition. While this sounds fairly useful, browser support is currently poor. Only Firefox and Safari have native implementations at the writing of this book. But we can make use of this method in all browsers by writing our own shim:

{title="Conditionally creating an Array.prototype.find shim", lang=javascript}
~~~~~~~
if (!Array.prototype.find) {
  Array.prototype.find =
    function(callback, ctx) {
      for (var i = 0; i < this.length; i++) {
        var el = this[i];
        if (callback.call(ctx, el, i, this)) {
          return this[i];
        }
     }
  };
}
~~~~~~~

If (and only if) the browser does not natively implement `Array.prototype.find`, the above code will register an implementation. So, with the above shim, you may use `Array.prototype.find` just [as it is presented in the ECMAScript 2015 specification][es2015find] in any browser, even Internet Explorer 6! Essentially, the shim, just like a native implementation, will iterate over all items in the array until it finds one that satisfies the passed predicate function, or until it runs out of elements to examine. For each element in the array, the passed predicate function is called, passing the current array element, the current array index, and finally the entire array. Notice that the `ctx` argument, which is optional, allows the calling code to specify an alternate value of `this` (also known as context) to be used by the predicate function. If this context argument is omitted, the actual context of the passed predicate function will be the "global object", which happens to be the `window` object if this code is executing in a browser. An array element satisfies the predicate function if the function returns a truthy value. The shim will return the element that satisfies the predicate, or undefined if _no_ element is satisfactory.

Using our shim, the following function will return the element in the array with a name property of "foobar". This happens to be the third element in the array.

{title="using the Array.prototype.find shim", lang=javascript}
~~~~~~~
function findFoo() {
  return [
    {name: 'one'},
    {name: 'two'},
    {name: 'foobar'},
    {name: 'four'}
  ].find(function(el) {
    return el.name === 'foobar';
  });
}
~~~~~~~


## The final word

jQuery isn't part of the future of web development, but neither are any of the other large and currently popular libraries or JavaScript frameworks. Libraries come and go – the browser's API and JavaScript will outlive them all. The future of the web, and of your career as a web developer, is codified in the web and ECMAScript specifications. These specifications are rapidly evolving – they are quickly catching up to the libraries. Native solutions to common problems usually result in improved performance and increased convenience.

There is nothing necessarily wrong with using wrapper libraries like jQuery. However, it is imperative that you have a proper understanding, not only of the code that jQuery itself depends on, but also of the reasons why you have chosen to use it. You do not really _need_ jQuery, but if you still _want_ to use it, be sure to take note of the situations where its use is acceptable and of those where you may want to consider forgoing this particular abstraction.


[^jsperfchrome41]: Based on tests against Chrome 41 using jQuery 1.11.2.

[^jsperfchrome42]:  Chrome 42 using jQuery 1.11.2.

[es2015find]: http://people.mozilla.org/~jorendorff/es6-draft.html#sec-array.prototype.find

[es5]: http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf

[fetch]: https://fetch.spec.whatwg.org/

[json2]: https://github.com/douglascrockford/JSON-js

[jsperfhide]: http://jsperf.com/jquery-hide-vs-set-attr

[jsperfjqueryselector]: http://jsperf.com/jquery-loop-vs-complex-selector

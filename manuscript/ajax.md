# Ajax Requests: Dynamic Data and Page Updates {#ajax-requests}

AJAX, or **A**synchronous **J**avaScript **a**nd **X**ML, is a feature provided by the web API that allows data to be updated or retrieved from a server without reloading the entire page. This capability was absent from the browser initially. This time marked the infancy of the web, and with it came along a less-than-ideal user experience that resulted in a fair amount of redundant bytes circulated between client and server. The inefficiency of this model was compounded by the fact that internet bandwidth was extremely limited by today's standards. Back in 1999 when Microsoft first introduced [`XMLHTTP` as an ActiveX control in Internet Explorer 5.0][xhr-init], about [95% of internet users were limited by a 56 kbps or slower][broadband-99] dial-up connection.

`XMLHTTP` was a proprietary JavaScript object implemented by Microsoft, and it represented a huge leap in both web development technology and user experience. It was the first full-featured transport for focused client/server communication without the need to update the current page. Previously, the entire page needed to be reloaded even if only a small segment of the data on the page needed to be refreshed. This all changed with the advent of AJAX communication. The initial API for this new transport matches it's modern-day cousin - `XMLHTTPRequest`. Essentially, this object allows a developer to construct a `new` transport instance, send a GET, POST, PUT, or DELETE request to any endpoint (on the same domain), and then programmatically retrieve the status and message-body of the server's response. While the aging `XMLHTTPRequest` will eventually be completely replaced by the [Fetch API][fetch-whatwg], it thrived unopposed and mostly unchanged for about 15 years.


## Mastering the concepts of ajax communication

It is critical to understand a few key concepts when dealing with ajax communication:

1. Asynchronous operations
2. HyperText Transfer Protocol, also know as HTTP
3. JSON, URL encoding, and multipart form encoding
4. The Same Origin Policy

The first 2 items will be dealt with directly in this section, in addition to an introduction to web sockets (which are not as important as some other concepts, but still potentially useful). The last two in the above list will be addressed later on in this chapter.


### Async is hard

Based on my extensive experience with ajax communication, along with observations of other developers struggling with this piece of the web API, the most attractive attribute of this feature is also its most confusing. JavaScript does not abstract asynchronous operations nearly as well as other more traditional languages, such as Java. On top of the historical lack of intuitive native support for tasks that occur in separate threads (such as ajax requests), there are currently three different common ways to account for these types of out-of-band operations. These methods include callbacks, promises, and asynchronous functions. While native support for asynchronous operations _has_ improved over time, most developers still must explicitly deal with these types of tasks, which can be challenging due to the fact that it often requires all surrounding code to be structured accordingly. This often makes the software developer's job of accounting for these operations awkward and the resulting code complex. This of course adds risk and potentially more bugs to the underlying application.

Callbacks will be discussed in this chapter, as will promises, to a lesser degree. Promises will be covered in much more detail in the upcoming [Async Tasks](#async-tasks) chapter, along with asynchronous functions, which is a feature provided by ECMAScript 7 that aims to make dealing with asynchronous operations, such as ajax requests, surprisingly easy. However, most developers don't have the luxury of using ES7 async functions, so the reality of dealing with ajax request remains that you must embrace their asynchronous nature instead of hiding from it. This is quite mind-bending initially. Even after you have successfully grasped this concept, expect frequent frustration in less-than-trivial situations, such as when dealing with nested asynchronous requests. If this is not already clear through previous experience, you will likely come to realize this complexity as you complete this chapter. Still, this concept is perhaps the most important of all to master when working with ajax requests.


### HTTP

The primary protocol used to communicate between a browser and a server is HTTP, which is an acronym for HyperText Transfer Protocol. Tim Berners-Lee, also know as the father of the internet, created the [first official HTTP specification][http-w3c-91] in 1991. This first version was designed alongside HTML and the [first web browser](#history-of-browsers) with one method - GET. When a page was requested by the browser, a GET request would be sent, and the server would respond with the HTML that makes up the requested page, which the web browser would then render. Before ajax was introduced as a complementary specification, HTTP was mostly limited to this workflow.  

Though HTTP started with just one method - GET - several more were added over time. Currently, HEAD, POST, PUT, DELETE, and PATCH are all part of the current specification - version 2 - which is maintained by the Internet Engineering Task Force (IETF) as [RFC 7540][rfc7540]. GET requests are expected to have an empty message body (request payload), with a response that describes the resource referenced in the request URI (Universal Resource Indicator). It is a "safe" method, such that no changes to the resource should be made server-side as a result of handling this request. HEAD is very similar to GET, except is returns an empty message body. However, HEAD is useful such that it does include a response header - `Content-Length` - with a value equal the the number of bytes that would be transferred had the request been GET instead. This is useful to, for example, check the size of a file without actually returning the entire file. HEAD, as you might expect, is also a "safe" method.

DELETE, PUT, POST, and PATCH, are _not_ safe, in that they are expected to possibly change the associated resource on the server. Of these four "unsafe" methods, two of them - PUT and DELETE - are considered to be "idempotent", which means that they will always produce the same result even they are called multiple times. PUT is commonly used to replace a resource, while DELETE is, obviously, used to remove a resource. PUT requests are expected to have a message body that describes the updated resource content, while DELETE is _not_ expected to have a payload. POST differs from PUT in that it will create a new resource. Finally, PATCH, which is [a relatively new HTTP request method][patch-rfc5789], allows a resource to be modified in very specific ways. The message body of this request describes exactly how the resource should be modified. PATCH differs from the PUT method in that it does not entirely replace the referenced resource.

All ajax requests will use one of the methods described above to communicate dynamically with a server. Note that new methods, such as PATCH, may not be supported in older browsers. Later on in this chapter, I'll go into more detail regarding proper use of these methods, and how the ajax request specification can can be used with these methods to produce a highly dynamic web application.


### Expected and unexpected responses

An understanding of the protocol used to communicate between client and server is a fundamentally important concept. With this comes the ability to send requests from client to server, but the _response_ to these requests is equally important. Request have headers and optionally a message-body (payload), while responses are made up of three parts - the response message-body, headers, and a status code. The status code is unique to responses, and is generally accessible when analyzing the response associated with JavaScript-initiated request. Status codes are usually three digits and can be generally classified based on the most significant digit, starting with 200. 200-level status codes are indicative of success, 300-level is used for redirects, while 400 and 500-level statuses indicate some sort of error. These are all formally [defined in great detail in RFC 2616][status-rfc2616].

Just as it is important to handle code exceptions with a `try`/`catch` block, it is equally important to address exceptional responses. While 200-level responses are often expected, or at least desired, you must also account for unexpected or undesired responses, such as 400/500-level, or even responses with a status of `0` (which may happen if the request is terminated due to a network error or the server returns a completely empty response). I have observed that it appears to be common to simply ignore exceptional conditions, and this is not limited to handling of HTTP responses. In fact, I am guilty of this myself.


### Web Sockets

Web sockets are a relatively new web API feature, compared to traditional ajax requests. They were first standardized in 2011 by the IETF (Internet Engineering Task Force) in [RFC 6455][rfc6455] and are currently supported by all [modern browsers](#modern-browsers), with the exception of Internet Explorer 9. Web sockets differ from pure HTTP requests in a number of ways, most notably their lifetime. While HTTP requests are normally very short-lived, web socket connections are meant to remain open for the life of the application instance or web page. a web socket connection starts as an HTTP request, which is required for the initial handshake. But after this handshake is complete, client and server are free to exchange data at will in whatever format they have agreed upon. This web socket protocol allows for truly real-time communicate between client and server. While web sockets will not be explored in more depth in this chapter, I felt it was useful to at least mention them, as they _do_ account for another method of JavaScript-initiated asynchronous communication between browser & server.


## Sending GET, POST, DELETE, PUT, and PATCH requests

jQuery provides first-class support for ajax requests through the appropriately named `ajax()` method. Through this method, you can send ajax requests of any type, but jQuery also provides alias for many standardized HTTP request methods - such as `get()` and `post()` - which save you a few keystrokes. The web API provides a single object - `XMLHttpRequest` - to send asynchronous requests of any type from browser to server.

A simple GET request to a server endpoint using jQuery, with a trivial response handler, looks something like this:

{title="send GET request & handle the response - jQuery", lang=javascript}
~~~~~~~
$.get('/my/name').then(
  function success(name) {
    console.log('my name is ' + name);
  }
);
~~~~~~~

W> Note that the console object is not available in Internet Explorer 9 and older unless the developer tools are open. Also, the above example does not handle error responses, only success.

Above, we are sending a GET request to the "/my/name" server endpoint, and expecting a plaintext response containing a name value, which we are then printing to the browser console. Please note that I am not handling error responses, simply to keep this example simple. You should _always_ account for exceptional situations when dealing with ajax responses in production-level applications. jQuery provides several ways to deal with responses, but I will focus specifically on the one demonstrated above, which is a very simple example of a promise. I'll talk about promises, which are a standardized part of JavaScript, in [the Async Tasks chapter](#async-tasks) coming up later.

The same request, sent without jQuery and usable in all browsers, is a bit more typing, but certainly not too difficult:

{title="send GET request & handle the response - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('GET', '/my/name');
xhr.onload = function() {
  console.log('my name is ' + xhr.responseText);
};
xhr.send();
~~~~~~~

The `onload` property is where we can easily set our response handler. From here, we can be sure the response has completed, and then can determine if the request was successful and get a handler on the response data. All of this is available on the instance of `XMLHTTPRequest` we created and assigned to the `xhr` variable. From the above code, you may be a bit disappointed that the web API doesn't support promises like jQuery. That _was_ true, up until [the `fetch` API][fetch-whatwg] was created by [the WHATWG](#whatwg-vs-w3c). The Fetch API provides a modern native replacement for the aging `XMLHTTPRequest` transport, and it is currently supported by Firefox and Chrome, with more browser support on the horizon. Let's look at this same example using `fetch`:

{title="send GET request & handle the response - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
fetch('/my/name').then(function(response) {
  return response.text();
}).then(function(name) {
  console.log('my name is ' + name);
});
~~~~~~~

The above code not only embraces the promises specification, but also removes a lot of the boilerplate commonly seen with `XMLHttpRequest`. You'll see more examples of `fetch` throughout this chapter.

Many developers who learned web development through a jQuery lens probably think that this library is doing something magical and complex when you invoke the `$.ajax` method. That couldn't be further from the truth. All of the heavy lifting is done by the browser via the `XMLHttpRequest` object. jQuery's `ajax` is just a wrapper around `XMLHttpRequest`. Using the browser's built-in support for ajax requests isn't very difficult, as you'll see in a moment. Even cross-origin requests are not only simple without jQuery, you'll see how they are actually _easier_ without jQuery.


### Examples of appropriate uses of each method


### Hands-on: Maintaining a list of names



## Encoding requests and reading encoded responses


### URL encoding


### JSON encoding


### Multipart encoding


### Plain text



## Uploading and manipulating files


### Uploading files in older browsers


### Uploading files in newer browsers


### Reading and creating files



## Cross-domain communication: an important topic


### Why is this so complicated?


### CORS


### Communication between differing browsing contexts


### Content Security Policy


[broadband-99]: http://www.websiteoptimization.com/bw/0403/
[fetch-whatwg]: https://fetch.spec.whatwg.org/
[http-w3c-91]: http://www.w3.org/Protocols/HTTP/AsImplemented.html
[patch-rfc5789]: https://tools.ietf.org/html/rfc5789
[rfc6455]: https://tools.ietf.org/html/rfc6455
[rfc7540]: https://httpwg.github.io/specs/rfc7540.html
[status-rfc2616]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10
[xhr-init]: https://blogs.msdn.microsoft.com/ie/2006/01/23/native-xmlhttprequest-object/

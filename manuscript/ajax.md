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

The above code not only embraces the promises specification, but also removes a lot of the boilerplate commonly seen with `XMLHttpRequest`. You'll see more examples of `fetch` throughout this chapter. Note that any of the above examples are identical for HEAD requests, with the exception of the method specifier.

Many developers who learned web development through a jQuery lens probably think that this library is doing something magical and complex when you invoke the `$.ajax` method. That couldn't be further from the truth. All of the heavy lifting is done by the browser via the `XMLHttpRequest` object. jQuery's `ajax` is just a wrapper around `XMLHttpRequest`. Using the browser's built-in support for ajax requests isn't very difficult, as you'll see in a moment. Even cross-origin requests are not only simple without jQuery, you'll see how they are actually _easier_ without jQuery.


### Sending POST requests

I demonstrated above how to GET information from a server endpoint using jQuery, `XMLHttpRequest`, and `fetch`. But what about some of the other available HTTP methods? Instead of GETting a name, suppose you would like to add a new name to the server. The most appropriate method for this situation is POST. The name to add will be included in our request payload, which is a common place to include this sort of data when sending a POST. To keep this simple, we'll sending the name using a MIME type of text/plain. I'll cover more advanced encoding techniques later on in this chapter. I'll also omit response handling code here as well. You'll see more complete examples in the upcoming hands-on section. This section is meant to demonstrate the differences between the various approaches when sending requests.

{title="send POST request to add a name - jQuery", lang=javascript}
~~~~~~~
$.ajax({
  method: 'POST',
  url: '/user/name',
  contentType: 'text/plain',
  data: 'Mr. Ed'
});
~~~~~~~

We're using jQuery's generic `ajax` method so we can specify various parameters, such as the request's `Content-Type` header. This will send a POST request with a plaintext body containing the text "Mr. Ed". We must explicitly specify the `Content-Type` in this case since jQuery will otherwise set this header to "application/x-www-form-urlencoded", which is not what we want.

The same POST request using `XMLHttpRequest` looks like this:

{title="send POST request to add a name - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('POST', '/user/name');
xhr.send('Mr. Ed');
~~~~~~~

Sending this request without jQuery is actually _less_ lines of code. By default, [the `Content-Type` is set to "text/plain" by `XMLHttpRequest` in this case][xhr-default-content-type], so we don't need to mess with any request headers. We can conveniently pass the body of our request as a parameter to the `send` method, as illustrated above.

If your goal is to embrace the latest and greatest web standards, you can sending this POST using `fetch`:

{title="send POST request to add a name - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
fetch('/user/name', {
  method: 'POST',
  body: 'Mr. Ed'
});
~~~~~~~

Sending this request looks similar to jQuery, but without a lot of the boilerplate. Like `XMLHTTPRequest`, [`fetch` intelligently sets the request `Content-Type` based on the request payload][fetch-default-content-type], so we do not need to specify this request header.


### Sending PUT requests

While POST requests are often used to create a new resource, PUT requests typically update an existing one. For example, PUT would be most appropriate for replacing information about an existing product. Another example involves uploading a large file in chunks. The first request, creating the resource, would be a POST. Subsequent requests that contain each piece of the file would use PUT, as each chunk is effectively be an update to the server-side representation of the file. The URI of the request identifies the resource to be updated with the new information located in the body. To simply illustrate sending a PUT request using jQuery, `XMLHttpRequest`, and `fetch`, I'll demonstrate updating a mobile phone number for an existing user record.

{title="send PUT request to update a user record - jQuery", lang=javascript}
~~~~~~~
$.ajax({
  method: 'PUT',
  url: '/user/1',
  contentType: 'text/plain',
  data: /* complete user record including new mobile number */
});
~~~~~~~

This PUT request using jQuery looks almost identical to the previously illustrated POST, with the exception of the `method` property. This user is identified by their ID, which happens to be 1. You likely won't be surprised to see that sending a PUT with `XMLHttpRequest` is similar to the previous example as well:

{title="send PUT request to update a user record - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('PUT', '/user/1');
xhr.send(/* complete user record including new mobile number */);
~~~~~~~

The Fetch API, as expected, provides the most concise approach:

{title="send PUT request to update a user record - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
fetch('/user/1', {
  method: 'PUT',
  body: /* complete user record including new mobile number */
});
~~~~~~~


### Sending DELETE requests

DELETE requests are similar to PUTs in that the resource to be acted upon is specified in the request URI. The main difference, even though this is not clearly mandated by [RFC 7231][rfc7231], DELETE requests typically do _not_ contain a message body. The [IETF document refers to the possibility that a message body may in fact cause problems for the requestor][rfc7231-delete]:

>A payload within a DELETE request message has no defined semantics; sending a payload body on a DELETE request might cause some existing implementations to reject the request.

This means that the only safe way to specify parameters, apart from the resource to be acted upon, is to include them as query parameters in the request URI. Other than this exceptional case, DELETE requests are sent in the same manner as PUTS. And, like a PUT request, DELETE requests are idempotent. Remember that an idempotent request is one that behaves the same regardless of the number of times it is called. It would be quite surprising if multiple calls to delete the same resource resulted in, for example, removal of a different resource depending on the request index.

A request to remove a resource, using jQuery, looks like this:

{title="send DELETE request to remove a user record - jQuery", lang=javascript}
~~~~~~~
$.ajax({
  method: 'DELETE',
  url: '/user/1'
});
~~~~~~~

...and the same simple request, using `XMLHttpRequest`, can be achieved with only an extra line of code:

{title="send DELETE request to remove a user record - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('DELETE', '/user/1');
xhr.send();
~~~~~~~

Finally, we can send this DELETE request natively in Firefox and Chrome using the ever-so-elegant Fetch API (or in any browser with a [polyfill](#shims-and-polyfills) for `fetch` [maintained on GitHub][[fetch-polyfill]]):

{title="send DELETE request to remove a user record - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
fetch('/user/1', {method: 'DELETE'});
~~~~~~~

Sending this request with `fetch` is so simple that we can easily write the entire thing in one line without losing readability.


### Sending PATCH requests

As mentioned earlier in this chapter, PATCH requests are relatively new on the HTTP scene, and they are used to update a _portion_ of an existing resource. Take our previous PUT request example, where we only want to update a user's mobile phone number, but have to include all other user data in our PUT request as well. For small records, this may be fine, but for records of substantial size, it can not only be a waste of bandwidth, but also a poor use of server processing power (determining which properties have actually changed). For this situation, we can use PATCH requests.

Let's revisit the PUT example where we need to update an existing user's mobile number, this time with PATCH. A jQuery approach - using a simple plaintext-based key-value message body to indicate the property to change along with the new property value - would look like this:

{title="send PATCH request to update a user's mobile number - jQuery", lang=javascript}
~~~~~~~
$.ajax({
  method: 'PATCH',
  url: '/user/1',
  contentType: 'text/plain',
  data: 'mobile: 555-5555'
});
~~~~~~~

Remember that we can use any format we choose for the body of the PATCH request to specify the data to update, as long as client and server are in agreement. If we prefer to use `XMLHTTPRequest`, the same request looks like this:

{title="send PATCH request to update a user's mobile number - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('PATCH', '/user/1');
xhr.send('mobile: 555-5555');
~~~~~~~

For the sake of completeness, I'll show you how to send the exact same request using the Fetch API as well:

{title="send PATCH request to update a user's mobile number - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
fetch('/user/1', {
  method: 'PATCH',
  body: 'mobile: 555-5555'
});
~~~~~~~


## Encoding requests and reading encoded responses

In the previous section, I touched on the simplest of all encoding methods - text/plain - which is the Multipurpose Internet Mail Extension (better known as MIME type) for unformatted text that makes up an HTTP request or response. The simplicity of plain text is both a blessing and a curse. That is, it is easy to work with, but only appropriate for very small and simple cases. The lack of any standardized structure limits its expressiveness and usefulness. For more complex (and more common) requests, there are more appropriate encoding types. In this section, I'll discuss three other MIME types - application/x-www-form-urlencoded, application/json, and multipart/form-data. At the end of this section, you will be not only familiar with these additional encoding methods, but will also be able to understand how to encode and decode messages without jQuery, especially when sending HTTP requests and parsing responses.


### URL encoding

URL encoding can take place in the request's URL, or in the message body of the request/response. The MIME type of this encoding scheme is application/x-www-form-urlencoded and data consists of simple key/value pairs. Each key and value is separated by an equal side (`=`), while each pair is separated by an ampersand (`&`). But the encoding algorithm is much more than this. Keys and values may be further encoded, depending on their character makeup. Non-ASCII characters, along with some reserved characters, are replaced with a percent character (`%`) followed by the character's ASCII code. This is [further defined in the HTML forms section of the W3C HTML5 specification][forms-html5]. This description is arguably a bit over-simplistic, but certainly appropriate and comprehensive enough for the purposes of this chapter. If you'd like to learn more about the encoding algorithm for this MIME type, please have a look at the spec, though it is a bit dry and may take a few reads to properly parse.

For GET and DELETE requests, URL encoded data should be included at the end of the URI, since these request methods typically should not include payloads. For all other requests, the body of the message is the most appropriate place for your URL encoded data. In this case, the request or response must include a `Content-Type` header of "application/x-www-form-urlencoded" - the MIME type of the encoding scheme. URL encoded messages are expected to be relatively small, especially when dealing with GET and DELETE requests due to [real-world URI length restrictions in place on browsers and servers][uri-length-limit-stackoverflow]. While this encoding method is more elegant than text/plain, the lack of hierarchy means that these messages are also limited in their expressiveness to some degree.

jQuery's API provides a function that takes an object and turns it into a URL encoded string - `$.param`. For example, if we wanted to encode a couple simple key/value pairs into a URL-encoded string, our code would look like this:

{title="URL encode values - jQuery", lang=javascript}
~~~~~~~
$.param({
  key1: 'some value',
  'key 2': 'another value'
});
~~~~~~~

The above line would produce a string of "key1=some+value&key+2=another+value". The specification for the application/x-www-form-urlencoded MIME type _does_ declare that spaces are reserved characters that should be converted to the "plus" character. However, in practice, the ASCII character code is also acceptable. So, the same couple of key/value pairs can _also_ be expressed as "key1=some%20value&key%202=another%20value". You'll see an example of this in use when I cover URL encoding with the web API.

So, if we wanted to create a new user with 3 properties - name, address, and phone - we could send a POST request to our server with a URL-encoded request body containing the information for the new user. This request would look like this with jQuery:

{title="send URL-encoded POST request to add a name - jQuery", lang=javascript}
~~~~~~~
$.ajax({
  method: 'POST',
  url: '/user/name',
  data: {
    name: 'Mr. Ed',
    address: '1313 Mockingbird Lane',
    phone: '555-555-5555'
  }
});
~~~~~~~

jQuery does admittedly make this relatively intuitive, as it allows you to pass in a JavaScript object describing the new user. There is no need to use the `$.param` method. As I mentioned earlier, jQuery's `$.ajax` API method assumes a `Content-Type` of "application/x-www-form-urlencoded", and it encodes the value of your `data` property to automatically to match this assumption. You don't have to think about encoding or encoding types at all in this case.

Even though the web API _does_ require you to be conscious of the encoding type _and_ it requires you encode your data before sending the request, these tasks are not overly complicated. I've already showed you how jQuery allows you to encode a string of text to "application/x-www-form-urlencoded" - using `$.param` - and you can accomplish the same without jQuery using the `encodeURI` and `encodeURIComponent` methods available on global namespace. These methods are defined in the ECMAScript specification, and have been available since the [ECMA-262 3rd edition specification][ecmascript3], completed in 1999.

Both `encodeURI` and `encodeURIComponent` perform the same general task - URL encode a string. But they each determine _which_ parts of the string to encode a bit differently, so they are tied to specific use-cases. `encodeURI` is meant to be used for a complete URL, such as "http://raynicholus.com?first=ray&last=nicholus", _or_ a string of key-value pairs separated by an ampersand (`&`), such as "first=ray&last=nicholus". However, `encodeURIComponent` is meant to only be used on a single value that needs to be URL encoded, such as "Ray Nicholus" or "Mr. Ed". If you use `encodeURIComponent` to encode the full URL listed earlier in this paragraph then the colon, forward slashes, question mark, and ampersand will all be URL encoded, which is probably not what you want (unless the entire URL is itself a query parameter).

Looking back at the simple URL-encoding example with jQuery a little while back in this section, we can encode the same data with the web API using `encodeURI`:

{title="URL encode values - web API - all browsers", lang=javascript}
~~~~~~~
encodeURI('key1=some value&key 2=another value');
~~~~~~~

A couple things about the output of `encodeURI` above, which produces "key1=some%20value&key%202=another%20value". First, notice that, while jQuery replaced spaces with the plus sign (`+`), `encodeURI` (and `encodeURIComponent`) replaces spaces with "%20". This is perfectly valid, but a notable difference. Second, while jQuery allows you to express the data to be encoded as a JavaScript object, `encodeURI` requires you separate the keys from the values with an equals sign (`=`) and the key-value pairs with an ampersand (`&`). Taking this a bit further, we can duplicate the same POST request we sent above that adds a new name, first using `XMLHttpRequest`:

{title="send URL-encoded POST request to add a name - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest(),
    data = encodeURI(
      'name=Mr. Ed&address=1313 Mockingbird Lane&phone=555-555-5555');
xhr.open('POST', '/user/name');
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send(data);
~~~~~~~

Another notable difference between the `XMLHTTPRequest` route and jQuery's `$.ajax` is that we must set the request header's `Content-Type` header as well. jQuery sets this for us by default, and it is necessary to let the server know how to decode the request data. Luckily, `XMLHttpRequest` provides us with a method for setting request headers - the aptly named `setRequestHeader`.


### JSON encoding


### Multipart encoding



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
[ecmascript3]: http://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262,%203rd%20edition,%20December%201999.pdf
[fetch-default-content-type]: https://fetch.spec.whatwg.org/#body-mixin
[fetch-polyfill]: https://github.com/github/fetch
[fetch-whatwg]: https://fetch.spec.whatwg.org/
[forms-html5]: http://www.w3.org/TR/html5/forms.html#application/x-www-form-urlencoded-encoding-algorithm
[http-w3c-91]: http://www.w3.org/Protocols/HTTP/AsImplemented.html
[patch-rfc5789]: https://tools.ietf.org/html/rfc5789
[rfc6455]: https://tools.ietf.org/html/rfc6455
[rfc7231]: https://tools.ietf.org/html/rfc7231
[rfc7231-delete]: https://tools.ietf.org/html/rfc7231#section-4.3.5
[rfc7540]: https://httpwg.github.io/specs/rfc7540.html
[status-rfc2616]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10
[uri-length-limit-stackoverflow]: http://stackoverflow.com/questions/417142/what-is-the-maximum-length-of-a-url-in-different-browsers
[xhr-default-content-type]: http://www.w3.org/TR/XMLHttpRequest#dom-xmlhttprequest-send
[xhr-init]: https://blogs.msdn.microsoft.com/ie/2006/01/23/native-xmlhttprequest-object/

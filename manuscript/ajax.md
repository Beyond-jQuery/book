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
  url: '/user',
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
xhr.open('POST', '/user');
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send(data);
~~~~~~~

Another notable difference between the `XMLHTTPRequest` route and jQuery's `$.ajax` is that we must set the request header's `Content-Type` header as well. jQuery sets this for us by default, and it is necessary to let the server know how to decode the request data. Luckily, `XMLHttpRequest` provides us with a method for setting request headers - the aptly named `setRequestHeader`.

We can gain some of the same benefits seen previously with the Fetch API, but we _still_ need to do our own encoding. No worries, since that can be accomplished without much trouble:

{title="send URL-encoded POST request to add a name - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
var data =
  encodeURI('name=Mr. Ed&address=1313 Mockingbird Lane&phone=555-555-5555');
fetch('/user', {
  method: 'POST',
  headers: {'Content-Type': 'application/x-www-form-urlencoded'},
  body: data
})
~~~~~~~

Just as with `XMLHttpRequest`, the `Content-Type` header must also be specified here, since the default `Content-Type` for `fetch`-initiated requests is also "text/plain". But, again, the Fetch API allows ajax requests to be constructed in a more elegant and condensed form, similar to the solution that jQuery has provided for some time now. While `fetch` is only supported in Firefox, Chrome (and by default Opera), there are open cases to add support to [Safari][fetch-safari-bug] and [Microsoft Edge][fetch-edge-status]. In the near future, `XMLHttpRequest` will be an artifact of history, and `fetch`, which rivals jQuery's ajax support, will be the native transport of choice.


### JSON encoding

JavaScript Object Notation, better known as JSON, is considered to be a "data-interchange language" (as described on json.org). If this obscure description leaves you a bit puzzled, don't feel bad, it's not a particularly useful summary. Think of JSON as a JavaScript object turned into a string. There a bit more to explain, but this is a reasonable high-level definition in my opinion. In web development, this is particularly useful if a browser-based client wants to easily send a JavaScript object to a server endpoint. The server can, in turn, respond to requests from this client, also supplying JSON in the response body, and the client can easily convert this into a JavaScript object to allow easy programmatic parsing and manipulation. While "application/x-www-form-urlencoded" requires data be expressed in a flat format, "application/json" allows data to be expressed in a hierarchical format. A key can have many sub-keys, and those sub-keys can have child keys as well. In this sense, JSON is more more expressive than URL-encoded data, which itself is more expressive and structured than plain text.

In case you are not already familiar with this common data format, let's represent a single user in JSON:

{title="a user record expressed as a JSON string", lang=javascript}
~~~~~~~
{
  "name": "Mr. Ed",
  "address": "1313 Mockingbird Lane",
  "phone": {
    "home": "555-555-5555"
    "mobile": "444-444-4444"
  }
}
~~~~~~~

Notice that JSON gives us the power to easily express sub-keys, a feature that is lacking with plaintext and URL-encoded strings. Mr. Ed as two phone numbers, and with JSON we can elegantly associate both of these numbers with with a parent "phone" property. So, how can you convert a JSON string to a JavaScript object and back again using jQuery? First off, jQuery doesn't provide an API method to turn strings into objects. This is likely due to the fact that JavaScript provides a simple and intuitive API to solve this problem already. More on that soon. But what about converting a JavaScript object into a proper JSON string? For this, jQuery does provide something - `$.parseJSON()`. There isn't much need for these methods in the context of ajax requests, so I won't bother demonstrating them. Extending our ajax example from the previous section, let's add a new name record to our server using jQuery, this time with a JSON-encoded payload:

{title="send JSON-encoded POST request to add a name - jQuery", lang=javascript}
~~~~~~~
$.ajax({
  method: 'POST',
  url: '/user',
  contentType: 'application/json',
  data: JSON.stringify({
    name: 'Mr. Ed',
    address: '1313 Mockingbird Lane',
    phone: {
      home: '555-555-5555',
      mobile: '444-444-4444'
    }
  })
});
~~~~~~~

Notice that our code is looking a bit more ugly than the previous URL-encoded example. Two reasons for this, one being that we must explicitly specify the `Content-Type` header via a `contentType` property, an abstraction that isn't really much help. Second, we must leave the comfortable and familiar jQuery API to convert our JavaScript object into JSON. Since jQuery provides no API method to accomplish this (and why would it?) we must make use of the `JSON` object, which has been standardized as part of the ECMAScript specification. The `JSON` object provides a couple methods for converting JSON strings to JavaScript object, and back again. [It first appeared in the ECMAScript 5.1 specification][json-es51], which means it is supported in Node.js as well as all modern browsers, including Internet Explorer 8. The `stringify` method, used in the above jQuery POST example, takes the user record, which is represented as a JavaScript object, and converts it into a proper JSON string. This is needed before we can send the record to our server.

If you'd like to send a GET request that receives JSON data, you can do that quite simply using `getJSON`:

{title="receive JSON-encoded data - jQuery", lang=javascript}
~~~~~~~
$.getJSON('/user/1', function(user) {
  // do something with this user JavaScript object
});
~~~~~~~

What about sending the previous POST request without jQuery? First, with `XMLHttpRequest`:

{title="send JSON-encoded POST request to add a name - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest(),
    data = JSON.stringify({
      name: 'Mr. Ed',
      address: '1313 Mockingbird Lane',
      phone: {
        home: '555-555-5555',
        mobile: '444-444-4444'
      }
    });
xhr.open('POST', '/user');
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.send(data);
~~~~~~~

No surprises with XHR. This attempt looks very similar to the URL-encoded POST request we sent in the last section, with the exception of stringifying our JavaScript object, and setting an appropriate `Content-Type` header. As you have already seen, we must address the same issues whether we are using jQuery or not.

But sending a JSON-encoded resquest is only half of the required knowledge. We must be prepared to receive and parse a JSON response too. That looks something like this with `XMLHTTPRequest`:

{title="receive JSON-encoded data - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('GET', '/user/1');
xhr.onload = function() {
  var user = JSON.parse(xhr.responseText);
  // do something with this user JavaScript object
};
xhr.send();
~~~~~~~

Notice that we're sending a GET request for user data, and expecting our server to return this record in a JSON-encoded string. Above, we're leveraging the `onload` funtion, which will be called when the request completes. At that point, we can grab the response body via the `responseText` property on our XHR instance. To turn this into a proper JavaScript object, we must make use of the other method on the JSON object - `parse`. In modern browsers (with the exception of Internet Explorer), receiving JSON data with `XMLHttpRequest` is even easier:

{title="receive JSON-encoded data - web API - all modern browsers except IE", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('GET', '/user/1');
xhr.onload = function() {
  var user = xhr.response;
  // do something with this user JavaScript object
};
xhr.send();
~~~~~~~

The above example assumes that the server has included a `Content-Type` header of "application/json". This lets `XMLHttpRequest` know what to do with the response data. It ultimately converts it to a JavaScript object and makes the converted value available on the `response` property of our `XMLHttpRequest` instance.

Finally, we can use the Fetch API to add this new user record to our server, again using a JSON-encoded request:

{title="send JSON-encoded POST request to add a name - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
fetch('/user', {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  body: JSON.stringify({
    name: 'Mr. Ed',
    address: '1313 Mockingbird Lane',
    phone: {
      home: '555-555-5555',
      mobile: '444-444-4444'
    }
  })
});
~~~~~~~

This is unsurprising and straightforward, but what about receiving a JSON-encoded response from our server? Let's send a simply GET request using `fetch` for a user record, with the expectation that our server will respond with a user record encoded as a JSON string:

{title="receive JSON-encoded data - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
fetch('/user/1').then(function(request) {
    return request.json();
  }).then(function(userRecord) {
      // do something with this user JavaScript object
  });
~~~~~~~

The Fetch API provides our promissory callback with [a `Request` object][fetch-request]. Since we are expected JSON, we call the `json()` method on this object, which itself returns a `Promise`. Note that the `json()` method is actually defined on [the `Body` interface][fetch-body]. The `Request` interface implements the `Body` interface, so we have access to methods on both interfaces here. By returning that promise, we can chain another promissory handler and expect to receive the JavaScript object representation of the response payload as a parameter in our last success callback. Now we have our user record from the server. Pretty simple! Again, if promises are still a bit murky, I'll cover them in great detail later in the [async tasks](#async-tasks) chapter.


### Multipart encoding

Another common encoding scheme, usually associated with HTML form submissions, is multipart/form-data, also known as multipart encoding. The algorithm for this method of passing data is formally defined by the IETF in [RFC 2388][rfc2388]. The use of this algorithm in the context of HTML forms us further described by the W3C in [the HTML specification][multipart-html5]. Non-ASCII characters in a multipart/form-data message do _not_ have to be escaped. Instead, each piece of the message is split into fields, and each field is housed inside of a multipart boundary. Boundaries as separated by unique IDs that are generated by the browser, and they are guaranteed to be unique among all other data in the request, as the browser analyzes the data in the request to ensure a conflicted ID is not generated. A field in a multipart encoded message is often an HTML `<form>` field. Each field will be housed inside of its own multipart boundary. Inside of each boundary is a header, where metadata about field (such as its name/key), along with a body, where the field _value_ lives.

Consider the following form:

{title="simple HTML form used to illustrate multipart/form-data messages", lang=html}
~~~~~~~
<form action="my/server" method="POST" enctype="multipart/form-data">
  <label>First Name:
    <input name="first">
  </label>

  <label>Last Name:
    <input name="last">
  </label>

  <button>Submit</button>
</form>
~~~~~~~

When the submit button is hit, any entered data will be submitted to the server. Let's say the user enters "Ray" into the first text input, and "Nicholus" into the second text input. After hitting submit, request body may look something like this:

{title="multipart/form-data message body generated by browser for above form", lang=text}
~~~~~~~
-----------------------------1686536745986416462127721994
Content-Disposition: form-data; name="first"

Ray
-----------------------------1686536745986416462127721994
Content-Disposition: form-data; name="last"

Nicholus
-----------------------------1686536745986416462127721994--
~~~~~~~

The server knows how to find each form field by looking for the unique ID marking each section in the request body. This ID is included by the browser in the `Content-Type` header, which in this case is "multipart/form-data; boundary=---------------------------1686536745986416462127721994". Notice that the MIME type of the message body is separated by the multipart boundary ID with a semicolon.

But HTML form submissions are not the _only_ instance where we might want to encode our request message using the multipart/form-data MIME type. Since this MIME type is trivial to implement in all server-side languages, it is probably a safe choice for transmitting key/value pairs from client to server. But, above all else, multipart encoding is perfect for mixing key/value pairs with binary data (such as files). I'll talk more about uploading files in the next section.

So how can we sending a multipart encoded request using jQuery's `$.ajax` method?  As you'll see shortly, it's ugly, and the layer of abstraction that jQuery normally provides is incomplete in this case as you must delegate directly to the web API anyway. Continuing with some of the previous examples, let's send a new user record to our server, a record consisting of a user's name, address, and phone number:

{title="sending a multipart encoded request using jQuery - all modern browsers except IE9", lang=javascript}
~~~~~~~
var formData = new FormData();
formData.append('name', 'Mr. Ed');
formData.append('address', '1313 Mockingbird Lane');
formData.append('phone', '555-555-5555');

$.ajax({
  method: 'POST',
  url: '/user',
  contentType: false,
  processData: false,
  data: formData
});
~~~~~~~

In order to send a multipart encoded ajax request, we must send a `FormData` object that contains our key/value pairs, and the browser takes care of the rest. There is no jQuery abstraction here; you must make use of the web API's `FormData` directly. Note that `FormData` isn't supported in Internet Explorer 9. But there isn't a real problem with using `FormData`. Though this lack of abstraction is a hole in jQuery's blanket, `FormData` is relatively intuitive and quite powerful. In fact, you can pass it a `<form>` element and key/value pairs will be created for you, ready for asynchronous submission to your server. Mozilla Developer Network has a [great writeup on `FormData`][formdata-mdn]. You should read it for more details.

The biggest problem with sending MPE requests with jQuery is the obscure options that must be set to make it work. `processData: false`? What does that even mean? Well, if you don't set this option, jQuery will attempt to turn `FormData` into a URL-encoded string. As for `contentType: false`, this is required to ensure that jQuery doesn't insert its own Content-Type header. Remember from the section introduction that the browser _must_ specify the Content-Type for you as it includes a calculated multipart boundary ID used by the server to parse the request.

The same request with plain 'ole `XMLHttpRequest` contains no surprises, and, quite frankly, isn't any less intuitive than jQuery's solution:

{title="sending a multipart encoded request - web API - all modern browsers except IE9", lang=javascript}
~~~~~~~
var formData = new FormData(),
    xhr = new XMLHttpRequest();

formData.append('name', 'Mr. Ed');
formData.append('address', '1313 Mockingbird Lane');
formData.append('phone', '555-555-5555');

xhr.open('POST', '/user');
xhr.send(formData);
~~~~~~~

In fact, using XHR results in _less_ code, and we don't have to include non-sensical options such as `contentType: false` and `processData: false`. As expected, the Fetch API is even more intuitive:

{title="sending a multipart encoded request - web API - Chrome and Firefox only", lang=javascript}
~~~~~~~
var formData = new FormData();
formData.append('name', 'Mr. Ed');
formData.append('address', '1313 Mockingbird Lane');
formData.append('phone', '555-555-5555');

fetch('/user', {
  method: 'POST',
  body: formData
});
~~~~~~~

See? If you can look Beyond jQuery, just a bit, you'll find that the web API is not always as scary as some make it out to be. In this case, jQuery's API suffers from inelegance.


## Uploading and manipulating files

Asynchronous file uploading, [a topic that I have quite a bit of experience with][fineuploader], is yet another example of how jQuery can often fail to effectively wrap the web API and provide users with an experience that justifies use of the library. This is indeed a complex topic, and while I can't cover everything related to file uploads here, I'll be sure to explain the basics and show how files are be uploaded using jQuery, `XMLHttpRequest`, and `fetch` both in [modern browsers](#modern-browsers) _and_ [ancient browsers](#ancient-browsers). In this specific and unusual case, note that Internet Explorer 9 is excluded from the definition of "modern browsers". The reason for this will become clear soon.


### Uploading files in ancient browsers

Before we get into uploading files in older browsers, let's define a very important term for this section: "browsing context". A browsing context can be a `window` or a `iframe`, for example. So if we have a `window`, and an `iframe` inside of this `window`, we have two browsing contexts - the parent `window`, and the child `iframe`.

The _only_ way to upload files in ancient browsers, including Internet Explorer 9, is to include an `<input type="file">` element inside of a `<form>` and submit this form. By default, the server's response to this form submit replaces the current browsing context. When working with a highly dynamic single page web application, this is unacceptable. We need to be able to upload files in older browsers and still maintain total control over the current browsing context. Unfortunately, there is no way to prevent a form submit from replacing the current browsing context. But we can certainly create a child browsing context where we submit the form, and then monitor this browsing context to determine when our file has been uploaded by listening for changes.

The approach described above can be implemented quite easily, simply by asking the form to target an `<iframe>` in the document. To determine when the file has finished uploading, attach an "onload" event handler to the `<iframe>`. To demonstrate this approach, we'll need to make a few assumptions in order to make this relatively painless. First, imagine our primary browsing context looks like this:

{title="HTML fragment that represents our initial browser context with a file input element", lang=html}
~~~~~~~
<form action="/upload"
      method="POST"
      enctype="multipart/form-data"
      target="uploader">

  <input type="file" name="file">

</form>

<iframe name="uploader" style="display: none;"></iframe>
~~~~~~~

Notice that the `enctype` attribute is set to "multipart/form-data". You may remember from the previous section that a form with file input elements must generate a multipart encoded request in order to properly communicate the file bytes to the server.

Second assumption - we are given a function - `upload()` - that is called when the user selects a file via the file input element. I'm not going to cover this specific detail now, since we haven't yet covered event handling. I'll [discuss events](#browser-events) later in this book.

Ok, so how do we make this happen with jQuery?

{title="ajax uploading in ancient browsers + IE9 - jQuery", lang=javascript}
~~~~~~~
function upload() {
  var $iframe = $('iframe'),
      $form = $('form');

  $iframe.on('load', function() {
    alert('file uploaded!')
  });

  $form.submit();
}
~~~~~~~

We _could_ have accomplished a lot more with JavaScript/jQuery here, if we needed to, such as setting the `target` attribute. If we were working with nothing more than a single file input element, we could have also dynamically created the form and moved the file input inside of it as well. But none of that was necessary since our markup contained everything we needed already. How much work and complexity has jQuery saved us from here? Let's take a look at the non-jQuery version for a comparison:

{title="ajax uploading in ancient browsers + IE9 - web API - all browsers", lang=javascript}
~~~~~~~
function upload() {
   var iframe = document.getElementsByTagName('iframe')[0],
       form = document.getElementsByTagName('form')[0]

   iframe.onload = function() {
     alert('file uploaded!');
   }

   form.submit();
}
~~~~~~~

jQuery hasn't done much at all for us. The web API solution is almost identical to the initial jQuery code. In both cases, we must select the iframe and form, attach an `onload` handler that does something when the upload has completed, and the submit the form. In both cases, our primary browsing context/window remains untouched. The server's response is buried in our hidden `<iframe>`. Pretty neat!


### Uploading files in modern browsers

There is a much more modern way to upload files asynchronously, and this is possible in all modern browsers, with the exception of Internet Explorer 9. The ability to upload files via JavaScript is possible due to the [File API][fileapi-w3c], _and_ [`XMLHttpRequest` Level 2][xhr2-w3c]. Both of these elements of the web API are standardized by W3C specifications. jQuery does _nothing_ to make uploading files easier. The modern native APIs that bring file uploading the browser are elegant, easy to use, and powerful. jQuery doesn't so much to attempt to provide a layer of abstraction here, and actually makes file uploading a bit awkward.

A typical workflow involves the following steps:

1. User selects one or more files via a `<input type="file" multiple>` element. Note that the `multiple` [boolean attribute](#boolean-attributes) allows the user to select multiple files, provided that the browser supports this attribute.
2. JavaScript is used to specify a "change" event listener, which is called when the user selects one or more files.
3. When the "change" listener is invoked, grab the file or files from the `<input type="file">` element. These are made available as [`File` objects][file-w3c], which extend the [`Blob` interface][blob-w3c].
4. Upload the `File` objects using your ajax transport of choice.

Since we haven't covered [events](#browser-events) yet, assume a function exists that, when called, signals that our user has selected one or more files on the `<input type="file">` that we are monitoring. The goal is to upload these files. And to keep this example focused and simple, _also_ assume that the user is only able to select _one_ file. This means our `<input type="file">` element will _not_ include a `multiple` boolean attribute. File uploading in modern browsers with jQuery can be accomplished, given the environment I just described, like this:

{title="ajax uploading in modern browsers (except IE9) - jQuery", lang=javascript}
~~~~~~~
function onFileInputChange() {
  var file = $('input[type="file"]')[0].files[0];

  $.ajax({
    method: 'POST',
    url: '/uploads',
    contentType: false,
    processData: false,
    data: file
  });  
}
~~~~~~~

The above code will send a POST request to an "/uploads" endpoint, and the request body will contain the bytes of the file our user selected. Again we must use the obscure `contentType: false` option to ensure that jQuery leaves the Content-Type header alone so that it may be set by the browser to reflect the MIME type of the file. Also, `processData: false` is needed to prevent jQuery from encoded the `File` object, which destroys the file we are trying to upload. We also could have included the file in a `FormData` object and uploaded that instead. This becomes a better option if we need to upload multiple files in a single request, or if we want to easily include other form data alongside the upload file or files.

Without jQuery, using `XMLHttpRequest`, file uploading is actually much simpler:

{title="ajax uploading - web API - modern browsers except IE9", lang=javascript}
~~~~~~~
function onFileInputChange() {
  var file = document.querySelector('input[type="file"]').files[0],
      xhr = new XMLHttpRequest();

  xhr.open('POST', '/uploads');
  xhr.send(file);
}
~~~~~~~

Just as the jQuery example, we're grabbing the selected `File` from the file input's `files` property, which is included as part of the File API, and sending it to our endpoint by passing it into the `send()` method, which supports `Blob`s as of `XMLHttpRequest` level 2.

File uploading is also possible with the Fetch API. Let's take a look:

{title="ajax uploading - web API - Firefox and Chrome", lang=javascript}
~~~~~~~
function onFileInputChange() {
  var file = document.querySelector('input[type="file"]').files[0];

  fetch('/uploads', {
    method: 'POST',
    body: file
  });
}
~~~~~~~


### Reading and creating files

Generally speaking, developers comfortable with jQuery often attempt to solve _all_ of their frontend development problems with jQuery. They sometimes fail to see the web beyond this library. When developers become dependent on this safety net, this can often lead to frustration when a problem is not addressable through jQuery. This is the [oppressive magic](#oppressive-magic) I wrote about in chapter 1. You just saw how jQuery, at best, provides almost no help when uploading files. Suppose you want to read a file, or even create a new one or modify an existing one to be sent to a server endpoint? This is an area where jQuery has absolutely zero coverage. For reading files, you must rely on the [`FileReader` interface[filereader-w3c], which is defined in the File API. Creation of "files" browser-side rely on the [`Blob` constructor][blob-w3c].

The simplest `FileReader` example, which is sufficient for demonstration purposes here, is to read a text file to the console. Suppose a user selected this text file via a `<input type"file">` and the text `File` object is sent to a function for output. The code required to read this file and output it to the developer tools console would involve the following code:

{title="read a text file and output it to the console - all modern browsers except IE9", lang=javascript}
~~~~~~~
function onTextFileSelected(file) {
  var reader = new FileReader();

  reader.onload = function() {
    console.log(reader.result);
  }

  reader.readAsText(file);
}
~~~~~~~

No jQuery needed or even possible to read files. And why would you need it? Reading files is pretty easy! Suppose you want to take the same text file and then append some text to the end of the file before uploading it to your server? Surprisingly, this is pretty easy too:

{title="add text to an existing file - all modern browsers except IE9", lang=javascript}
~~~~~~~
function onTextFileSelected(file) {
  var modifiedFile = new Blob([file, 'hi there!'], {type: 'text/plain'});
  // ...send modifiedFile to uploader
}
~~~~~~~

The `modifiedFile` above is a copy of the selected file with the text "hi there!" added to the end. This was accomplished in a grand total of one line of code.


## Cross-domain communication: an important topic

As more and more logic is offloaded to the browser, it is becoming common for web applications to pull data from multiple APIs, some of which exist as part of third-party services. A great example of this is a web application (like [Fine Uploader][fineuploader]) that uploads files directly to an Amazon Web Services (AWS) Simple Storage Service (S3) bucket. Server-to-server cross-domain requests are simple and without restrictions. But the same is not true for cross-domain requests initiated from the browser. For the developer that wants to develop a web application that sends files directly to S3 from the browser, an obstacle lies in the way: [the same origin policy][sop]. This policy places restrictions on requests initiated by JavaScript. More specifically, request made by `XMLHttpRequest` between domains are prohibited. For example, sending a request to https://api.github.com from https://mywebapp.com is prevented by the browser because of the same origin policy. While this restriction is in place for added security, this seems like a major limiting factor. How can you make legitimate requests from domain A domain to domain B without funneling them through a server on domain A first? The next two sections will cover two specific approaches to accomplish this. After that, I'll show you how to communicate between iframes that belong to differing domains, something that the same origin policy also restricts.


### The early days (JSONP)

The same origin policy prevents scripts from initiating requests outside the domain of their current browsing context. While this covers ajax transports such as `XMLHttpRequest`, elements such as `<a>`, `<img>`, and `<script>` are _not_ bound by the same origin policy. JavaScript Object Notation with Padding, or JSONP, exploits one of these exceptions to allow scripts to make cross-origin GET requests.

If you're not familiar with JSONP, the name may be a bit misleading. There is actually no JSON involved here at all. It's a very common misconception that JSON must be returned from the server when the client initiates a JSONP call, but that's simply not true. Instead, the server returns a function invocation, which is _not_ valid JSON.

JSONP is essentially just an ugly hack that exploits the fact that `<script>`` tags that load content from a server are not bound by the same-origin policy. There needs to be cooperation and an understanding of the convention by both client and server for this to work properly. You simply need to point the `src` attribute of a `<script>`` tag at a JSONP-aware endpoint and include the name of an existing global function as a query parameter. The server will then construct a string representation that, when executed by the browser, will invoke the global function, passing in the requested data.

Exploiting this JSONP approach in jQuery is actually pretty easy. Say we want to get user information from a server on a different domain:

{title="JSONP - jQuery", lang=javascript}
~~~~~~~
$.ajax('http://jsonp-aware-endpoint.com/user/1', {
  jsonp: 'callback',
  dataType: 'jsonp'
}).then(function(response) {
  // handle user info from server
});
~~~~~~~

jQuery takes care of creating the `<script>` tag for us and also creates and tracks a global function. When the global function is called after the response from the server is recieved, jQuery passes that to our response handler above. This is actually a pretty nice abstraction. Completing the same task without jQuery is certainly possible, but not as nice:

{title="JSONP - web API - all browsers", lang=javascript}
~~~~~~~
window.myJsonpCallback = function(data) {
  // handle user info from server
};

var scriptEl = document.createElement('script');
scriptEl.setAttribute('src',
  'http://jsonp-aware-endpoint.com/user/1?callback=myJsonpCallback');
document.body.appendChild(scriptEl);
~~~~~~~

Now that you have this newfound knowledge, I suggest you forget it and avoid using JSONP altogether. It's proven to be [a potential security issue][jsonp-security]. Also, in modern browsers, CORS is a much better route. And you're in luck; CORS is feature in the next section. This JSONP section serves mostly as a history lesson and an illustration of how jQuery _was_ quite useful and important before the modern evolution of web specifications.


### Modern times (CORS)

CORS, which is short for Cross Origin Resource Sharing, is the more modern way to send ajax request between domains from the browser. CORS is actually a fairly involved topic and very commonly misunderstood even by seasoned web developers. While [the W3C specification][cors-w3c] can be hard to parse, Mozilla Developer Network [has a great explanation][cors-mdn]. I'm only going to touch on a few CORS concepts here, but MDN article is useful if you'd like to have a complete understanding of this topic.

With a reasonable understanding of CORS, sending a cross-origin ajax request via JavaScript is not particularly difficult in modern browsers. Unfortunately, the process is _not_ as easy in Internet Explorer 8 and 9. Cross-origin ajax requests are only possible via JSONP in IE7 and older. In all cases, jQuery offers zero assistance.

For modern browsers, all of the work is delegated server code. The browser does everything necessary client-side for you. Your code for a cross-origin ajax request in a modern browser is identical to a same-origin ajax request. So, I won't bother showing that in jQuery or native JavaScript.

CORS requests can be divided up into two distinct types: simple, and non-simple. Simple requests consists of GET, HEAD, and POST requests, with a Content-Type of "text/plain" or "application/x-www-form-urlencoded". Non-standard headers, such as "x-" headers, are not allowed in "simple" requests. These CORS requests are sent by the browser with an `Origin` header that includes the sending domain. The server must acknowledge that requests from this origin are acceptable. If not, the request fails. Non-simple requests consists of PUT and PATCH requests, as well as other Content-Types, such as "application/json". Also, non-standard headers, as you just learned, will mark a CORS request as "non-simple". In fact, even a GET or POST request can be non-simple if it, for example, contains non-standard request headers. A non-simple request must be "preflighted" by the browser. Non-simple cross-origin requests, such as PUT or POST/GET requests with an X-header (for example) could not be sent from a browser pre-CORS spec. So, for these types of requests, the concept of preflighting was written into the specification to ensure servers do not receive these types of non-simple cross-origin browser-based requests without explicitly opting in. In other words, if you don't want to allow these types of requests, you don't have to change your server at all. The preflight request that the browser sends first will fail, and the browser will never send the underlying request.

It is also important to know that cookies are _not_ sent by default with cross-origin ajax requests.  You must set the `withCredentials` flag on the `XMLHttpRequest` transport. For example:

{title="Sending a credentialed CORS request - jQuery - all modern browsers except IE9", lang=javascript}
~~~~~~~
$.ajax('http://someotherdomain.com', {
    method: 'POST',
    contentType: 'text/plain',
    data: 'sometext',
    beforeSend: function(xmlHttpRequest) {
        xmlHttpRequest.withCredentials = true;
    }
});
~~~~~~~

jQuery offers somewhat of a leaky abstraction here. We must set the `withCredentials` property on the underlying `xmlHttpRequest` that jQuery manages. The web API route is familiar, and, as expected, we must set the `withCredentials` flag in order to ensure cookies are sent to our server endpoint:

{title="Sending a credentialed CORS request - web API - all modern browsers except IE9", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest();
xhr.open('POST', 'http://someotherdomain.com');
xhr.withCredentials = true;
xhr.setRequestHeader('Content-Type', 'text/plain');
xhr.send('sometext');
~~~~~~~

jQuery actually becomes a headache to deal with when we need to send a cross-domain ajax request in IE8 or IE9. If you're using jQuery for this purpose, you are truly trying to fit a square peg into a round hole. To understand why jQuery is a poor fit for cross-origin requests in IE9 and IE8, it's important to consider a couple low-level points:

1. Cross-origin ajax requests in IE8 and IE9 can only be sent using the IE-proprietary `XDomainRequest` transport. I'll save the rant for why this was such a huge mistake by the IE development team for another book. Regardless, `XDomainRequest` is a stripped down version of `XMLHttpReqest`, and it _must_ be used when making cross-origin ajax requests in IE8 and IE9. There are ;significant restrictions restrictions imposed on this transport][xdr-msdn], such as an inability to send anything other than POST and GET request, and lack of API methods to set request headers or access response headers.

2. jQuery's `ajax` method (and all associated aliases) are just wrappers for `XMLHttpRequest`. It has a hard dependency on `XMLHttpRequest`. I mentioned this earlier in the chapter, but it's useful to point it out here again, given the context.

So, you need to use `XDomainRequest` to send the cross-origin request in IE8/9, but `jQuery.ajax` is hard-coded to use `XMLHttpRequest`. That's a problem, and resolving it in the context of jQuery is not easy. Luckily, for those dead-set on using jQuery for this type of call, there are a few plug-ins that will "fix" jQuery in this regard. Essentially, the plug-ins must override jQuery's ajax request sending/handling logic via the `$.ajaxTransport` method.

But, sending cross-origin ajax requests in IE8 and 9 is pretty simple without jQuery. In fact,even if you're a die-hard jQuery fan, you should do it this way:

{title="Determine if XDomainRequest is needed", lang=javascript}
~~~~~~~
if (new XMLHttpRequest().withCredentials === undefined) {
    var xdr = new XDomainRequest();
    xdr.open('POST', 'http://someotherdomain.com');
    xdr.send('sometext');
}
~~~~~~~


### Communication between differing browsing contexts (Web Messaging API)


[blob-w3c]: https://www.w3.org/TR/FileAPI/#dfn-Blob
[broadband-99]: http://www.websiteoptimization.com/bw/0403/
[cors-mdn]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS
[cors-w3c]: https://www.w3.org/TR/cors/
[ecmascript3]: http://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262,%203rd%20edition,%20December%201999.pdf
[fetch-body]: https://fetch.spec.whatwg.org/#body
[fetch-default-content-type]: https://fetch.spec.whatwg.org/#body-mixin
[fetch-edge-status]: https://dev.windows.com/en-us/microsoft-edge/platform/status/fetchapi
[fetch-polyfill]: https://github.com/github/fetch
[fetch-request]: https://fetch.spec.whatwg.org/#request-class
[fetch-safari-bug]: https://bugs.webkit.org/show_bug.cgi?id=151937
[fetch-whatwg]: https://fetch.spec.whatwg.org/
[file-w3c]: https://www.w3.org/TR/FileAPI/#dfn-file
[fileapi-w3c]: https://www.w3.org/TR/FileAPI/
[filereader-w3c]: https://www.w3.org/TR/FileAPI/#dfn-filereader
[fineuploader]: http://fineuploader.com
[formdata-mdn]: https://developer.mozilla.org/en-US/docs/Web/API/FormData
[forms-html5]: http://www.w3.org/TR/html5/forms.html#application/x-www-form-urlencoded-encoding-algorithm
[http-w3c-91]: http://www.w3.org/Protocols/HTTP/AsImplemented.html
[json-es51]: http://www.ecma-international.org/ecma-262/5.1/#sec-15.12
[jsonp-security]: http://security.stackexchange.com/a/23439
[multipart-html5]: https://www.w3.org/TR/html5/forms.html#multipart-form-data
[patch-rfc5789]: https://tools.ietf.org/html/rfc5789
[rfc2388]: https://www.ietf.org/rfc/rfc2388.txt
[rfc6455]: https://tools.ietf.org/html/rfc6455
[rfc7231]: https://tools.ietf.org/html/rfc7231
[rfc7231-delete]: https://tools.ietf.org/html/rfc7231#section-4.3.5
[rfc7540]: https://httpwg.github.io/specs/rfc7540.html
[sop]: https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy
[status-rfc2616]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10
[uri-length-limit-stackoverflow]: http://stackoverflow.com/questions/417142/what-is-the-maximum-length-of-a-url-in-different-browsers
[xdr-msdn]: http://blogs.msdn.com/b/ieinternals/archive/2010/05/13/xdomainrequest-restrictions-limitations-and-workarounds.aspx
[xhr-default-content-type]: http://www.w3.org/TR/XMLHttpRequest#dom-xmlhttprequest-send
[xhr-init]: https://blogs.msdn.microsoft.com/ie/2006/01/23/native-xmlhttprequest-object/
[xhr2-w3c]: https://www.w3.org/TR/XMLHttpRequest2/

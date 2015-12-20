# Ajax Requests: Dynamic Data and Page Updates {#ajax-requests}

AJAX, or **A**synchronous **J**avaScript **a**nd **X**ML, is a feature provided by the web API that allows data to be updated or retrieved from a server without reloading the entire page. This capability was absent from the browser initially. This time marked the infancy of the web, and with it came along a less-than-ideal user experience that resulted in a fair amount of redundant bytes circulated between client and server. The inefficiency of this model was compounded by the fact that internet bandwidth was extremely limited by today's standards. Back in 1999 when Microsoft first introduced [`XMLHTTP` as an ActiveX control in Internet Explorer 5.0][xhr-init], about [95% of internet users were limited by a 56 kbps or slower][broadband-99] dial-up connection.

`XMLHTTP` was a proprietary JavaScript object implemented by Microsoft, and it represented a huge leap in both web development technology and user experience. It was the first full-featured transport for focused client/server communication without the need to update the current page. Previously, the entire page needed to be reloaded even if only a small segment of the data on the page needed to be refreshed. This all changed with the advent of AJAX communication. The initial API for this new transport matches it's modern-day cousin - `XMLHTTPRequst`. Essentially, this object allows a developer to construct a `new` transport instance, send a GET, POST, PUT, or DELETE request to any endpoint (on the same domain), and then programmatically retrieve the status and message-body of the server's response. While the aging `XMLHTTPRequest` will eventually be completely replaced by the [Fetch API][fetch-whatwg], it thrived unopposed and mostly unchanged for about 15 years.


## Mastering the concepts of ajax communication

It is critical to understand a few key concepts when dealing with ajax communication:

1. Asynchronous operations
2. HyperText Transfer Protocol, also know as HTTP
3. JSON, URL encoding, and multipart form encoding
4. The Same Origin Policy

The first 2 items will be dealt with directly in this section, in addition to an introduction to web sockets (which are not as important as some other concepts, but still potentially useful). The last two in the above list` will be addressed later on in this chapter.


### Async is hard

Based on my extensive experience with ajax communication, along with observations of other developers struggling with this piece of the web API, the most attractive attribute of this feature is also its most confusing. JavaScript does not abstract asynchronous operations nearly as well as other more traditional languages, such as Java. On top of the historical lack of intuitive native support for tasks that occur in separate threads (such as ajax requests), there are currently three different common ways to account for these types of out-of-band operations. These methods include callbacks, promises, and asynchronous functions. While native support for asynchronous operations _has_ improved over time, most developers still must explicitly deal with these types of tasks, which can be challenging due to the fact that it often requires all surrounding code to be structured accordingly. This often makes the software developer's job of accounting for these operations awkward and the resulting code complex. This of course adds risk and potentially more bugs to the underlying application.

Callbacks will be discussed in this chapter, as will promises, to a lesser degree. Promises will be covered in much more detail in the upcoming [Async Tasks](#async-tasks) chapter, along with asynchronous functions, which is a feature provided by ECMAScript 7 that aims to make dealing with asynchronous operations, such as ajax requests, surprisingly easy. However, most developers don't have the luxury of using ES7 async functions, so the reality of dealing with ajax request remains that you must embrace their asynchronous nature instead of hiding from it. This is quite mind-bending initially. Even after you have successfully grasped this concept, expect frequent frustration in less-than-trivial situations, such as when dealing with nested asynchronous requests. If this is not already clear through previous experience, you will likely come to realize this complexity as you complete this chapter. Still, this concept is perhaps the most important of all to master when working with ajax requests.


### HTTP

The primary protocol used to communicate between a browser and a server is HTTP, which is an acronym for HyperText Transfer Protocol. Tim Berners-Lee, also know as the father of the internet, created the [first official HTTP specification][http-w3c-91] in 1991. This first version was designed alongside HTML and the [first web browser](#history-of-browsers) with one method - GET. When a page was requested by the browser, a GET request would be sent, and the server would respond with the HTML that makes up the requested page, which the web browser would then render. Before ajax was introduced as a complementary specification, HTTP was mostly limited to this workflow.  

Though HTTP started with just one method - GET - several more were added over time. Currently, HEAD, POST, PUT, DELETE, and PATCH are all part of the current specification - version 2 - which is maintained by the Internet Engineering Task Force (IETF) as [RFC 7540][rfc7540]. GET requests are expected to have an empty message body (request payload), with a response that describes the resource referenced in the request URI (Universal Resource Indicator). It is a "safe" method, such that no changes to the resource should be made server-side as a result of handling this request. HEAD is very similar to GET, except is returns an empty message body. However, HEAD is useful such that it does include a response header - `Content-Length` - with a value equal the the number of bytes that would be transferred had the request been GET instead. This is useful to, for example, check the size of a file without actually returning the entire file. HEAD, as you might expect, is also a "safe" method.

DELETE, PUT, POST, and PATCH, are _not_ safe, in that they are expected to possibly change the associated resource on the server. Of these four "unsafe" methods, two of them - PUT and DELETE - are considered to be "idempotent", which means that they will always produce the same result even they are called multiple times. PUT is commonly used to replace a resource, while DELETE is, obviously, used to remove a resource. PUT requests are expected to have a message body that describes the updated resource content, while DELETE is _not_ expected to have a payload. POST differs from PUT in that it will create a new resource. Finally, PATCH, which is [a relatively new HTTP request method][patch-rfc5789], allows a resource to be modified in very specific ways. The message body of this request describes exactly how the resource should be modified. PATCH differs from the PUT method in that it does not entirely replace the referenced resource.

All ajax requests will use one of the methods described above to communicate dynamically with a server. Note that new methods, such as PATCH, may not be supported in older browsers. Later on in this chapter, I'll go into more detail regarding proper use of these methods, and how the ajax request specification can can be used with these methods to produce a highly dynamic web application.


### Expected and unexpected responses


### Web Sockets



## Sending GET, POST, DELETE, PUT, and PATCH requests


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
[rfc7540]: https://httpwg.github.io/specs/rfc7540.html
[xhr-init]: https://blogs.msdn.microsoft.com/ie/2006/01/23/native-xmlhttprequest-object/

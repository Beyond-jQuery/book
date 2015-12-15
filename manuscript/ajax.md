# Ajax Requests: Dynamic Data and Page Updates {#ajax-requests}

AJAX, or **A**synchronous **J**avaScript **a**nd **X**ML, is a feature provided by the web API that allows data to be updated or retrieved from a server without reloading the entire page. This capability was absent from the browser initially. This time marked the infancy of the web, and with it came along a less-than-ideal user experience that resulted in a fair amount of redundant bytes circulated between client and server. The inefficiency of this model was compounded by the fact that internet bandwidth was extremely limited by today's standards. Back in 1999 when Microsoft first introduced [`XMLHTTP` as an ActiveX control in Internet Explorer 5.0][xhr-init], about [95% of internet users were limited by a 56 kbps or slower][broadband-99] dial-up connection.

`XMLHTTP` was a proprietary JavaScript object implemented by Microsoft, and it represented a huge leap in both web development technology and user experience. It was the first full-featured transport for focused client/server communication without the need to update the current page. Previously, the entire page needed to be reloaded even if only a small segment of the data on the page needed to be refreshed. This all changed with the advent of AJAX communication. The initial API for this new transport matches it's modern-day cousin - `XMLHTTPRequst`. Essentially, this object allows a developer to construct a `new` transport instance, send a GET, POST, PUT, or DELETE request to any endpoint (on the same domain), and then programmatically retrieve the status and message-body of the server's response. While the aging `XMLHTTPRequest` will eventually be completely replaced by the [Fetch API][fetch-whatwg], it thrived unopposed and mostly unchanged for about 15 years.


## Mastering the concepts of ajax communication


### Async is hard


### Expected and unexpected responses


### HTTP


### Web Sockets



## GET, POST, DELETE, and PATCH requests


### Appropriate use of each method


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
[xhr-init]: https://blogs.msdn.microsoft.com/ie/2006/01/23/native-xmlhttprequest-object/

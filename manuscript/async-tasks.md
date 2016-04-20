# Controlling Asynchronous Tasks (IN PROGRESS) {#async-tasks}

I would expect most web developers to be aware of the concept of asynchronous operations. In fact, I've already demonstrated some such tasks in earlier chapters. Admittedly, when this concept appeared previous to this chapter, I elected to avoid exploring in much detail. That is precisely the goal of this chapter - to cover the intricacies, challenges, and importance of async tasks in the world of front-end web development. While jQuery provides mostly adequate support for managing async tasks, I'll show you how limited jQuery is in the context, how the web API is advancing far past jQuery, and how jQuery has failed to properly implement an essential task management pattern until very recently.

Just so we are all on the same page throughout this chapter, let's be clear about the precise definition of the central topic here. An asynchronous call is one that is processed out-of-band. Still not clear? Sorry, let's try again. An asynchronous call is one that does not immediately return the requested result. Hmm, that's still a bit vague, isn't it? Ok, how about - An asynchronous call is one that, traditionally,  makes your code much more  difficult to manage and evolve. That last one is tongue-in-cheek, but it is likely a real opinion held by many, and rightfully so.

My inability to describe async tasks in a manner that can be easily understood by everyone is partially my fault, but it illustrates just how hard it can be to manage these types of operations with JavaScript. In my experience, proper use and management of asynchronous operations is one of the more frustrating tasks that web developers face. If the definitions I provided don't suffice, perhaps some real-world examples will clear things up.

So what are some _actual_ examples of async tasks? Where might we come across this concept in everyday web development? There are actually quite a few operations that are asynchronous by nature, and you may run into this when:

1. Handling responses to [ajax requests](#ajax-requests).
2. Reading files or blobs of data.
3. Requesting user input.
4. Passing messages between browsing contexts.

The most common async task is likely to involve ajax requests. Client/server communication is understandably asynchronous. When you send a request to a server endpoint from the browser using `XMLHttpReqest` or `fetch`, the server may respond in milliseconds, or perhaps even seconds after the request has been initiated. The point is, after you send the request, your application doesn't simply freeze until the request has returned. Code continues to execute. The user continues to be able to manipulate the page. So you must register a function that is invoked by the browser once the server has properly responded to your request.

There is no telling exactly when the function purposed with handling the server response will be executed, and that's "ok". Your application and code must be structured to account for the fact that this data will not be available until some unknown point in the future. Indeed, some logic may depend on this response, and your code will need to be written accordingly. Managing a single request or asynchronous operation may not be particularly difficult. But at scale, with a number of requests and other async tasks in progress concurrently, or a series of async tasks that depend on each other, this becomes mighty hairy.

%% APIs: the benefits of async support
%% jQuery is _way_ behind the web API in this area
%% FYI: Some of the latter sections deal with bleeding edge web API specs. Exciting, but still lacking browser support. With compilers and shims, most of these can all be used now though.


## Callbacks: The traditional approach for controlling async operations

%% What are callbacks?

### Node.js: The champion of callbacks

%% Built into many APIs
%% Accepted convention

### Solving common problems with callbacks

%% Ajax request
%% User input


## Promises: An answer to async complexity

%% What is a promise?

### Why are callbacks flawed?

### Using Promises to simplify async operations

### jQuery's broken promise implementation

### Native browser support


## Async functions: An abstraction for async tasks

%% Short explanation of what async functions are without revealing too much

### The problem with bare promises
%% How can we handle async tasks without thinking about the async part?

### Async functions to the rescue
%% Rewriting our promises code w/ async functions

### Browser support
%% jQuery?
%% Supporting async functions in non-compliant browsers


## Async iterators: A better way to control asynchronous loops

%% What are async iterators (async functions + generators)

### An example of when we might find ourselves looping over async data
%% Solving this with bare promises
%% Solving this with async functions

### Making life easier w/ async iterators

### Browser support


## Handling streams of data or events with Observable

%% What is a data stream?
%% What is an event stream?

### What is an Observable?

### Handling and filtering data streams

### Handling and filtering event streams

### Browser support

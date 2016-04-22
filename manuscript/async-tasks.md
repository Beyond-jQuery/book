# Controlling Asynchronous Tasks {#async-tasks}

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

In a general sense, APIs benefit from supporting async operations. For example, consider a library that executes a provided function when a user chooses a file to be uploaded. The function can prevent the file from being uploaded by returning `false`. But what if the function must delegate to a server endpoint to determine if the file is valid? This is an asynchronous operation. If the library doesn't provide support for this type of task in its API, integration is limited to some degree. Another example - a library that maintains a list of contacts. The user is provided with the ability to delete a contact via a `<button>`. While this is  does not provide the best user experience, it is common to display a confirm dialog before _actually_ deleting the contact. This fictional library provides a function that is called before the delete operation occurs, and allows it to be ignored if the function returns `false`. If you want a confirm dialog that stops execution of your code before the user responds, you _could_ use the browser's built-in confirm dialog and then return `false` if the user elects to cancel the operation, but the native confirm dialog is barebones and ugly. It isn't an ideal choice for most projects, so you will need to provide your own styled dialog, which will be non-blocking. In other words, the library will need to account for the asynchronous nature of waiting for a user to decide if they are _really sure_ that the file should be deleted forever. These are just two examples of how important it may be to consider async support when building an API, but there are many more.

While this chapter deals with traditional and some relatively new methods for dealing with async functions, I will also cover some other solutions that can probably be classified as "bleeding edge". Some of the later sections of this chapter will even cover solutions that are only rudimentary [ECMAScript](#js-history) specifications at the moment. The reason for inclusion of these now futuristic specifications in this chapter is to provide you with an indication of how important dealing with the asynchronous nature of the web has become, and how the maintainers of JavaScript are doing their best to make a traditionally difficult concept to manage much easier. On a positive note, compilers and shims or libraries make all of the draft-level solutions later on in this chapter usable now, even before they have been adopted browsers and Node.js.


## Callbacks: The traditional approach for controlling async operations

The most traditional way to provide support asynchronous tasks is via a system of callback functions. Let's take the the contacts list library example we just discussed and apply a callback function to account for the fact that a `beforeDelete` handler function may need to ask the user to confirm the contact removal, which is an async operation without the built-in `window.confirm` dialog. The code may look seomthing like this:

{title="supporting an async task with a callback function", lang=javascript}
~~~~~~~
contactsHelper.register('beforeDelete', function(contact, callback) {
  confirmModel.open(
    'Delete contact ' + contact.name + '?',
    function(result) {
      if (result.cancel) {
        callback({cancel: true});
      }
    });
});
~~~~~~~

The above code registers for "beforeDelete" events. When the user clicks the delete button next to a contact, the function passed to the the "beforeDelete" handler is invoked. The contact to be deleted is passed to this function, along with a callback function. If the delete operation is to be ignored, an object with a `cancel` property set to `true` must be passed into this callback. The library will "wait" for this call before attempting to delete the contact. Note that this "waiting" does not involve blocking the UI thread, so any other code can continue to execute.

I'm assuming there is a modal dialog component with an `open` function that displays a delete confirm dialog to the user. The result of the user's input is passed into another callback function supplied to the `open` function. If the user clicks the "cancel" button on this dialog, the `result` object passed to this particular callback function will contain a `cancel` property with a value of `true`. At that point, the callback function passed to the "beforeDelete" callback function will be invoked, indicating that the file to be deleted should _not_ in fact be deleted.

Notice how the above code depends on a number of varying conventions. A number of non-standard conventions. In fact, there really aren't any standards associated with callback functions. The value or values passed to the callback are part of a contract defined by the supplier of the function. In this case, the conventions are similar enough between the modal callback and the "beforeDelete" callback, but that may not always be the case. While callbacks are a simple and well-supported way to account for async results, some of the problems with this approach may already be clear to you.


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

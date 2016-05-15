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


### Node.js & the error-first callback

While I haven't spent a lot of time discussing Node.js, it has come up a few times throughout this book. The [non-browsers section of the "Understanding the Web API & JS" chapter](#non-browsers) goes into a _bit_ of detail about this surprisingly popular server-side use JavaScript-based system. Node.js has long relied on callbacks to support asynchronous behavior across APIs. In fact, it has has popularized a very specific type of callback system - the "error-first" callback. This particular convention is _very_ common throughout the Node.js landscape, and can be found as part of the API in many major libraries, such as Express, Socket.IO, and request. It is arguably the most "standard" of all the various callback systems, though of course there is no _real_ standard, just conventions. Though some conventions are more popular than others.

Error-first callbacks require, as you might expect, an error to be passed as the first parameter to a supplied callback function. Usually, this error parameter is expected to be an `Error` object. The `Error` object has always been part of JavaScript, starting with the first ECMAScript specification published back in 1997. The `Error` object can be thrown in exceptionally cases, or passed around as a standard way to describe an application error. With error-first callbacks, an `Error` object can be passed as the first parameter to a callback if the related operation failed in some way. If the operation succeeded, `null` should be passed as the first parameter instead. This makes is easy for the callback function itself to determine if the operation failed. And if the related task did _not_ fail, subsequent arguments are used to supply relevant information to the callback function.

Don't worry if this is not entirely clear to you. You'll see error-first callbacks in action throughout the rest of this section as I feel this is the most elegant way to either signal an error _or_ deliver the requested information when supporting an asynchronous task via a system of callbacks.


### Solving common problems with callbacks {#solving-problems-with-callbacks}

Let's look at a simple example of a module that asks the user for their email address, which is an asynchronous operation:

{title="error-first callback example", lang=javascript}
~~~~~~~
function askForEmail(callback) {
  promptForText('Enter email:', function(result) {
      if (result.cancel) {
        callback(new Error('User refused to supply email.'))    
      }
      else {
        callback(null, result.text)
      }
  })
}

askForEmail(function(err, email) {
  if (err) {
    console.error('Unable to get email: ' + err.message)
  }
  else {
    // do something with the `email`...
  }
})
~~~~~~~

Can you figure out the flow of the above code? An error-first callback is passed in as the sole parameter when invoking the function that ultimately asks our user for their email address. If the user declines to provide one, an `Error` with a description of the situation is passed as the first parameter to our error-first callback. The callback logs this and moves on. Otherwise, the `err` argument is `null`, which signals to the callback function that we did indeed receive a valid response from our user - the email address - which is contained in the second argument to the error-first callback.

Another practical use of callbacks is to handle the result of an ajax request. Since the very first version of jQuery, it has been possible to pass in a callback function to be invoked when an ajax request succeeds. In the chapter on ajax requests, I demonstrated GET requests (among others). Here's an alternate version of [the first GET request](#get-post-delete-put-patch):

{title="callback on ajax request success - jQuery", lang=javascript}
~~~~~~~
$.get('/my/name', function (name) {
  console.log('my name is ' + name)
})
~~~~~~~

The second parameter is a success callback function, which jQuery will call with the response data if the request succeeds. But the above example only handles success. What if the request fails? Yet another way to write this _and_ account for both success and failure is to pass an object that contains the URL, success, and failure callback functions:

{title="callbacks on ajax request success & failure - jQuery", lang=javascript}
~~~~~~~
$.get({
  url: '/my/name',
  success: function(name) {
    console.log('my name is ' + name)  
  },
  error: function() {
    console.error('Name request failed!')
  }
})
~~~~~~~

The same section in the ajax requests chapter demonstrates making this call without jQuery. This solution _also_ relies on callbacks to signal success and failure:

{title="callbacks on ajax request success & failure - web API - all browsers", lang=javascript}
~~~~~~~
var xhr = new XMLHttpRequest()
xhr.open('GET', '/my/name')
xhr.onload = function() {
  if (xhr.status >= 400) {
    console.error('Name request failed!')
  }
  else {
    console.log('my name is ' + xhr.responseText)
  }
};
xhr.onerror = function() {
  console.error('Name request failed!')
};
xhr.send()
~~~~~~~

Callbacks certainly seem to be a reasonable way to register for the result of an asynchronous task. And this is indeed true for simple cases. But a system of callbacks becomes less appealing for more complex scenarios.


## Promises: An answer to async complexity

Before I discuss an alternative to callbacks, perhaps it would be prudent to _first_ point out some of the issues associated with depending on callbacks to manage async tasks. The first fundamental flaw in the callback system described in the previous section is evident in every method or function signature that supports this convention. When invoking a function that utilizes a callback to signal success or failure of an asynchronous operation, you must supply this callback as a method parameter. Any input values used by the method must also be passed as parameters. In this case, you are now passing input values _and_ managing the method's output all through method parameters. This is a bit non-intuitive and awkward. This callback contract also precludes any return value. Again, all work is done via method parameters.

Another issue with callbacks - there is _no_ standard, only conventions. Whenever you find yourself needing to invoke a method that executes some logic asynchronously and expects a callback to manage this process, it _may_ expect an error-first callback, but it also may _not_. And how can you possibly know? Since there is no standard for callbacks, you are forced to refer to the API documentation and pass the appropriate callback. Perhaps you must interface with multiple libraries, all of which expect callbacks to manage async results, all of which rely on different callback method conventions. Some may expect error-first callbacks. Others may include an error or status flag elsewhere when invoking the supplied callback. Some may not even account for errors at all!

Perhaps the biggest issue with callbacks becomes apparent when they are forced into non-trivial use. For example, consider a few asynchronous tasks that must run sequentially, each subsequent task depending on the result from the previous. To demonstrate such a scenario, imagine you need to send an ajax request to one endpoint to load a list of user IDs, then a request must be made to a server to load personal information for the first user in the list. After this, the user's info is presented on-screen for editing, and finally the modified record is sent back to the server. This whole process involves four asynchronous tasks, which each task depending on the result of the previous. How would we model this workflow with callbacks? It's not pretty, but it might look something like this:

{title="using callbacks to support a series of dependent async tasks", lang=javascript}
~~~~~~~
getUserIds(function(error, ids) {
  if (!error) {
    getUserInfo(ids[0], function(error, info) {
      if (!error) {
        displayUserInfo(info, function(error, newInfo) {
          if (!error) {
            updateUserInfo(id, info, function(error) {
              if (!error) {
                console.log('Record updated!')  
              }
              else {
                console.error(error)
              }
            })
          }  
          else {
            console.error(error)
          }
        })
      }
      else {
        console.error(error)
      }
    })
  }
  else {
    console.error(error)
  }
})
~~~~~~~

The above code is commonly referred to as "callback hell". Each callback function must be nested inside of the previous one in order to make use of its result. As you can see, the callback system does _not_ scale very well. Let's look at another example which further confirms this conclusion. This time, we need to send three files submitted for a product in three separate ajax requests to three separate endpoints concurrently. We need to know when all requests have completed and if one or more of these requests failed. Regardless of the outcome, we need to notify our user with the result. If we are stuck using error-first callbacks, our solution is a bit of a mess:

{title="using callbacks to monitor a series of dependent async tasks", lang=javascript}
~~~~~~~
var successfulRequests = 0

function handleCompletedRequest(error) {
  if (error) {
    console.error(error)
  }
  else if (++successfulRequests === 3) {
    console.log('All requests were successful!')
  }
}

sendFile('/file/docs', pdfManualFile, handleCompletedRequest)
sendFile('/file/images', previewImage, handleCompletedRequest)
sendFile('/file/video', howToUseVideo, handleCompletedRequest)
~~~~~~~

The above code isn't _awful_ but we had to create our own system to track the result of these concurrent operations. What if we had to track more than three async tasks? Surely there must be a better way!


### The first standardized way to harness async

The flaws and inefficiencies associated with relying on callback conventions often lead developers to look for other solutions. Surely some of problems and boilerplate common to this async handling approach can be solved in and packaged into a more standardized API. The "Promises" specification defines an API that achieves this very goal, and so much more.

Promises have been publicly discussed on the JavaScript front for some time. The first instance of a Promise-like proposal (that I am able to locate) was created by Kris Kowal. It dates back to mid-2011, and describes ["Thenable Promises"][uncommonjs-promises]. A couple lines from the introduction provide a good glimpse into the power of promises:

>An asynchronous promise loosely represents the eventual result of a function. A resolution can either be "fulfilled" with a value or "rejected" with a reason, corresponding by analogy to synchronously returned values and thrown exceptions respectively.

This loose proposal was, in part, used to form the [Promises/A+ specification][promises-aplus]. This specification has a number of implementations, many of which can be seen in various JavaScript libraries, such as [bluebird][bluebird], [Q][q], and [rsvp.js][rsvp.js]. But perhaps the more important implementation appeared in the [ECMAScript-262 6th Edition specification][es6-promise]. Remember from the ["Understanding the Web and JavaScript" chapter](#understanding-the-web) that the ECMAScript-262 standard defines a language specification implemented as JavaScript. The 6th edition of this spec was officially completed in 2015. At the writing of this chapter in "Beyond jQuery", the Promise object defined in this standard is available natively in all modern browsers, with the exception of Internet Explorer. Luckily, many lightweight [polyfills](#shims-and-polyfills) are available to fill in this gap.


### Using Promises to simplify async operations

So what exactly _are_ promises? What is a promise? You _could_ read through the ES6 or A+ specifications, but, like more formal language specifications, these are both a bit dry and perplexing. First and foremost a Promise, in the context of ECMAScript, is an object used to manage the result of an asynchronous operation. It bridges all of the gaps in a complex application left by traditional convention-based callbacks.

Once the overarching goal of promises is clear, it proved useful (for me) to read [Domenic Denicola's "States and Fates" article][states-and-fates-denicola]. From this document, we learn that promises have three states:

1. Pending: the initial state, before the associated operation has concluded.
2. Fulfilled: The associated operation monitored by the promise has completed without error.
3. Rejected: The associated operation has reached an error condition.

Domenic goes on to define a term that groups both the "fulfilled" and "rejected" states: "settled". So, a promise is initially pending, and then it is settled once it has concluded.

There are also two distinct "fates" defined in this document:

1. Resolved: A promise is resolved when it is fulfilled or rejected, _or_ when it has been redirected to follow another promise. An example of the latter condition can be seen when chaining asynchronous promise-returning operations together. More on that soon.
2. Unresolved: As you might expect, this means that the associated promise has not yet been resolved.

If you can understand the concepts described above, you are very close to mastering promises, and you will find working with the API defined in the A+ and ECMAScript-262 specifications much easier.


#### The anatomy of a `Promise`

A JavaScript promise is created simply by constructing a `new` instance of an A+-compliant `Promise` object, such as the one detailed in the ECMAScript 6 specification. The `Promise` constructor takes one argument - a function. This function itself takes _two_ arguments which are both functions that give the promise a resolved "fate" as described above. The first argument passed to the `Promise` constructor is a "fulfilled" function. This is to be called when the associated asynchronous operation completes successfully. When the "fulfilled" function is invoked, a value related to the completion of the promissory task should be passed. For example if a `Promise` is used to monitor an ajax request, the server response may be passed to this "fulfilled" function argument once the request completes successfully.

The _second_ argument passed to the `Promise` constructor's function parameter is a "reject" function. This function should be called when the promissory task has failed for some reason, and the reason describing the failure should be passed into this rejected function. Often, this will be an `Error` object. If an exception is thrown inside of the `Promise` constructor, this will automatically cause the "reject" function to be invoked with the thrown `Error` passed as an argument. Going back to the ajax request example - if the request were to fail, the "reject" function should be called, passing either a string description of the result, or perhaps the HTTP status code.

When a function returns a `Promise`, the caller can "observe" the result a couple of different ways. The most common way to handle a promissory return value is to call the `then` method on the promise instance. This method takes two parameters - both functions. The first functional parameter is invoked if the associated promise is fulfilled. As expected, if a value is associated with this fulfillment (such as a server response for an ajax request) it is passed to this first function. The second function parameter is invoked if the promise fails in some way. You may omit the second parameter if you are only interested in fulfillment (though this is generally unsafe to assume your promise will succeed). Additionally, you may specify a value of `null` or `undefined`, or any value that is not considered to be ["callable"][es6-callable] as the first argument if you are only interested in promise rejection. An alternative to this, which also lets you focus exclusively on the error case, is to call the `catch` method on the returned `Promise`. This `catch` method takes one argument - a function that is invoked when/if the associated promise errors.

The ES6 `Promise` object includes several other helpful methods, but one of the more useful non-instance methods is `all`, which allows you to monitor many promises at once. The `all` method returns a new `Promise` that is fulfilled ff all monitored promises are fulfilled, and rejects as soon as one of the monitored promises is rejected. The `Promise.race` method is very similar to `Promise.all`, the difference being that the `Promise` returned by `race` is fulfilled immediately when the first monitored `Promise` is fulfilled. It does not wait for all monitored `Promise` instances to be fulfilled first. One use for `race` could also apply to ajax requests. Imagine you were triggering an ajax request that persisted the same data to multiple redundant endpoints. All that is important is the success of one request, in which case `Promise.race` is more appropriate and much more efficient than waiting for all requests to complete with `Promise.all`.


#### Simple `Promise` examples

If the previous sections wasn't enough to properly introduce you to JavaScript promises, a few code examples should push you over the edge. In the above ["solving problems with callbacks" section](#solving-problems-with-callbacks), I provided a a couple blocks that demonstrated handling of async task results using callbacks. The first such example outlined a function that prompts the user to enter an email address in a dialog box - an asynchronous task. An error-first callback system was used to handle both successful and unsuccessful outcomes. The same example can be rewritten to make use of promises:

{title="a simple promise example - asking a user for an email address", lang=javascript}
~~~~~~~
function askForEmail() {
  return new Promise(function(fulfill, reject) {
    promptForText('Enter email:', function(result) {
        if (result.cancel) {
          reject(new Error('User refused to supply email.'))    
        }
        else {
          fulfill(result.text)
        }
    })
  })
}

askForEmail().then(
  function fulfilled(emailAddress) {
    // do something with the `email`...
  },
  function rejected(error) {
    console.error('Unable to get email: ' + err.message)
  }
)
~~~~~~~

In the above example rewritten to support promises, our code is much more declarative and straightforward. The `askForEmail` function returns a `Promise` that describes the result of the "ask the user for their email" task. When calling this function, we can intuitively handle both a supplied email address and an instance where the email is not provided by following a codified standard. Notice that we are still assuming that the `promptForText` function API is unchanged, but the code can be simplified even further if this function also returns a promise:

{title="a simple promise example - asking a user for an email address", lang=javascript}
~~~~~~~
function askForEmail() {
  return promptForText('Enter email:')
}

askForEmail().then(
  function fulfilled(emailAddress) {
    // do something with the `email`...
  },
  function rejected(error) {
    console.error('Unable to get email: ' + err.message)
  }
)
~~~~~~~

If `promptForText` returns a `Promise`, it _should_ pass the user-entered email address to the fulfilled function if an address is supplied, or a descriptive error to the rejected function if the user closes the dialog without entering an email address.  These implementation details are not visible above, but based on the `Promise` specification, this is what we should expect.

The other example in the callbacks section demonstrates the `onload` and `onerror` callbacks provided by `XMLHttpRequest`. Just to recap, `onload` is called when the request completes (regardless of the server response status code) and `onerror` is invoked if the request fails to complete for some reason (such as due to CORS or other network issues). As I mentioned in the [Ajax Requests](#ajax-requests) chapter, the Fetch API brings a replacement for `XMLHttpRequest` that makes use of the `Promise` specific to signal the result of an ajax request. I'll dive into a more complex example that makes use of `fetch` shortly, but first, let's write a wrapper around the `XMLHttpRequest` call from the callbacks section that presents a more elegant interface using promises:

{title="a reusable promise-wrapped XMLHttpRequest call", lang=javascript}
~~~~~~~
function get(url) {
  return new Promise(function(fulfill, reject) {
    var xhr = new XMLHttpRequest()
    xhr.open('GET', url)
    xhr.onload = function() {
      if (xhr.status >= 400) {
        reject('Name request failed w/ status code ' + xhr.status)
      }
      else {
        fulfill(xhr.responseText)
      }
    }
    xhr.onerror = function() {
      reject('Name request failed!')
    }
    xhr.send()  
  })
}

get('/my/name').then(
  function fulfilled(name) {
    console.log('Name is ' + name)
  },
  function rejected(error) {
    console.error(error)
  }
)
~~~~~~~

While the `Promise`-wrapped `XMLHttpRequest` doesn't simplify that code much, it gives us a great opportunity to generalize this GET request, which makes it more reusable. Also, our code that uses this new GET request method is easy to follow and magnificently readable and elegant. Both the success and failure conditions are a breeze to account for, and the logic required to manage this is wrapped away inside the `Promise` constructor function. Of course, we could have created a similar approach _without_ `Promise`, but the fact that this async task-handling mechanism is an accepted JavaScript language standard makes it all the more appealing.


#### Fixing "callback hell" with promises

Earlier in this section, I demonstrated one of the many issues with callbacks that presents itself in a non-trivial situation where consecutive dependent async tasks are involved. That particular example required retrieving all user IDs in the system, followed by retrieval of the user information for the first returned user ID,, and then displaying the info for editing in a dialog, followed by a callback to the server with the updated user information. This accounts for four separate but interdependent asynchronous calls. The first attempt to handle this made use of several nested callbacks, which resulted in a pyramid-style code solution, often referred to as "callback hell". Promises are an elegant solution to this problem, and callback hell is avoided entirely due to the ability to chain promises. Take a look at a rewritten solution that makes use of the `Promise` API:

{title="using promises to support a series of dependent async tasks", lang=javascript}
~~~~~~~
getUserIds()
  .then(function(ids) {
    return getUserInfo(ids[0])
  })
  .then(function(info) {
    return displayUserInfo(info)
  })
  .then(function(updatedInfo) {
    return updateUserInfo(updatedInfo.id, updatedInfo)
  })
  .then(function() {
    console.log('Record updated!')
  })
  .catch(function(error) {
    console.error(error)
  })
~~~~~~~

That's quite a bit easier to follow, isn't it! The flow of the async operations is probably apparent as well. Just in case it isn't, I'll walk you through it. I've contributed a fulfilled function for each of the four `then` blocks to handle specific successful async operations. The `catch` block at the end will be invoked if _any_ of the async calls fail. Note that `catch` is _not_ part of the A+ Promise specification, though it _is_ part of the ECMAScript 6 `Promise` spec.

Each async operation - `getUserIds`, `getUserInfo`, `displayUserInfo`, and `updateUserInfo` - returns a `Promise`. The fulfilled value for each async operation's returned `Promise` is made available to the fulfilled function on the subsequently chained `then` block. No more pyramids, no more callback hell, and a simple and elegant way to handle a failure of any call in the workflow.


#### Monitoring multiple related async tasks with promises

Remember the callbacks example from the start of this promises section that illustrated one approach to handling three separate ajax requests to three separate endpoints concurrently? We needed to know when all requests completed _and_ if one or more of them failed. The solution wasn't ugly, but it was verbose and contained a fair amount of boilerplate that could become cumbersome should we find ourselves in this situation often. I surmised that there must be a better solution to this problem, and there is! The `Promise` API brings a much more elegant solution, particularly with the `all` method, which allows us to easily monitor all three asynchronous tasks and react when they all complete successfully, or when one fails. Take a look at the rewritten Promise-ified code:

{title="using promises to monitor a series of dependent async tasks", lang=javascript}
~~~~~~~
Promise.all([
  sendFile('/file/docs', pdfManualFile, handleCompletedRequest),
  sendFile('/file/images', previewImage, handleCompletedRequest),
  sendFile('/file/video', howToUseVideo, handleCompletedRequest)
]).then(
  function fulfilled() {
    console.log('All requests were successful!')
  },
  function rejected(error) {
    console.error(error)
  }
)
~~~~~~~

The above solution assumes `sendFile` returns a `Promise`. With this being true, monitoring these requests becomes much more intuitive and lacks almost all of the boilerplate and ambiguity from the callbacks example. `Promise.all` takes an array of `Promise` instances and returns a new `Promise`. This new returned `Promise` is fulfilled when all of the `Promise` objects passed to `all` are fulfilled, or it is rejected if _one_ of these passed `Promise` objects is rejected. This is _exactly_ what we are looking for, and the `Promise` API provides this support to us natively.


### jQuery's broken promise implementation

Almost all of the code in this chapter has focused exclusively on the support for async tasks that is native to JavaScript. The rest of this chapter is going follow a similar pattern. This is mostly due to the fact that jQuery simply doesn't provide much in terms of powerful async support. The ECMAScript-262 standard is far ahead of jQuery in this regard. But since this book aims to explain much of the web API and JavaScript to those coming from a jQuery-centric perspective, I feel it is important to at least _mention_ jQuery in this section, since it _does_ have support for promises, but this support is, unfortunately, broken and completely non-standard in all released versions of jQuery (at the time this chapter was written). While these issues are scheduled to be fixed in jQuery 3.0, promises have suffered from some notable deficiencies in the library for quite some time.

There are _at least_ two serious implementation bugs in jQuery's promise implementation. Two deficiencies that make promises non-standard and frustrating to work with. The first deals with error handling. Suppose an `Error` is thrown inside of a promise's fulfilled function handler, part of a first `then` block. In order to catch this sort of issue, it is customary to register a rejected handler on a subsequent `then` block, chained to the first `then` block. Remember that each `then` block returns a _new_ promise. Your code may look something like this:

{title="handling errors thrown inside of an ES6 promise fulfilled function", lang=javascript}
~~~~~~~
someAsynTask
  .then(
    function fulfilled() {
      throw new Error('oops!')
    }
  )
  .then(null, function rejected(error) {
    console.error('Caught an error: ' + error.message)
  })
~~~~~~~

The code above will print an error log to the console that reads "Caught an error: oops!". But if the same pattern is implemented using [jQuery's `deferred` construct][jquery-deferred], the error will _not_ be caught by the chained rejected handler. Instead, it will remain uncaught. Valerio Gheri goes into much more detail in [his article on the subject][gheri-jquery-broken]. I'll leave it to you to read further if you are interested in more specifics regarding the issues with jQuery's promise error handling - it is not prudent to spend more time on this here.

The second major issue with jQuery's promise implementation breaks the expected order of operations. In other words, instead of observing the expected execution order of code alongside promise handlers, jQuery changes the order of execution to match the order in which the code _appears_ in the executable source. This is an overly simplistic explanation, but I'd like to avoid spending too much time on this jQuery-specific bug. If you'd like to read more, take a peek at [Valera Rozuvan's "jQuery Broken Promises Illustrated" article][rozuvan-jquery-broken]. The lesson here is simple - avoid jQuery's promises implementation. It is non-standard and filled with bugs.


### Native browser support

As I mentioned earlier, the `Promise` API is standardized as part of ECMAScript-262 6th edition. At the writing of this chapter, all [modern browsers](#modern-browsers), with the exception of Internet Explorer, implement promises natively. There are a number of Promises/A+ libraries available (such as RSVP.js, Q, and Bluebird) but I prefer a small and focused polyfill that makes the elements described in the ES6 specification available to non-compliant browsers (Internet Explorer). For this, I highly recommend the small and effective ["es6-promise"][es6-promise-polyfill] polyfill by Stefan Penner.


## Async functions: An abstraction for async tasks

The TC39 group that standardized promises in ECMAScript-262 6th edition has been working on a related specification that builds upon the existing `Promise` API. The [async functions specification][async-spec][tc39-process], also known as async/await, is not officially targeted for any specific ECMAScript-262 edition, though it seems likely that it will land in the 8th edition in 2017. At the writing of this chapter, it is currently sitting in stage 3, which is the second-to-last stage in the [TC39 specification acceptance process]. This means async functions are essentially complete and ready to be associated with a future formal ECMAScript-262 edition. There seems to be a lot of momentum and excitement surrounding async functions (and rightfully so). All things considered, async functions are likely to be standardized in the very near future, relatively speaking.

Async functions provide several features that make handling async operations incredibly easy. Instead of getting lost in a sea of conventions or async-specific API methods, they allow you to treat asynchronous code as if it was completely synchronous. This feature presents another feature - you can use the same traditional constructs and patterns that you have always used. Need to catch an error in the asynchronous method? Simply wrap it in a try/catch block. Want to return a value from an async function? Go ahead, return it! The elegance of async functions is a bit surprising at first, and web development will benefit enormously once they become more commonly used and understood.


### The problem with bare promises

%% we have to _think_ about async here, and deal with it using unnatural and non-traditional constructs
%% apis that return promissory values suffer from promise implementation noise
%% How can we handle async tasks without thinking about the async part?

### Async functions to the rescue

%% Rewriting our promises code w/ async functions
%% async/await isn't perfect - you still have to define functions as async, but the syntax is _much_ simpler and more elegant. You can use familiar & traditional patterns to handle both async and non-async code

### Browser support

%% no browser support
%% babel, typescript, etc


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


[async-spec]: https://tc39.github.io/ecmascript-asyncawait/

[bluebird]: https://github.com/petkaantonov/bluebird

[es6-callable]: http://www.ecma-international.org/ecma-262/6.0/#sec-iscallable

[es6-promise]: http://www.ecma-international.org/ecma-262/6.0/#sec-promise-objects

[es6-promise-polyfill]: https://github.com/stefanpenner/es6-promise

[gheri-jquery-broken]: https://thewayofcode.wordpress.com/2013/01/22/javascript-promises-and-why-jquery-implementation-is-broken/

[jquery-deferred]: https://api.jquery.com/jquery.deferred/

[promises-aplus]: https://github.com/promises-aplus/promises-spec

[q]: https://github.com/kriskowal/q

[rozuvan-jquery-broken]: http://valera-rozuvan.github.io/nintoku/jquery/promises/jquery-broken-promises-illustrated

[rsvp.js]: https://github.com/tildeio/rsvp.js

[states-and-fates-denicola]: https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md

[tc39-process]: https://tc39.github.io/process-document/

[uncommonjs-promises]: https://github.com/kriskowal/uncommonjs/blob/ea03e6d40430318b1d9821a181f3961bbf02eb12/promises/specification.md

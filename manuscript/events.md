# Browser Events {#browser-events}

Reacting to changes on a page is a big part of modern web application development. While this has always been possible to _some_ degree with anchor links and form submit buttons, the introduction of an events system made it possible to adjust to user input without reloading or changing the current page. You can probably see how this complements the ability to send [ajax requests](#ajax-requests) as well. Since Internet Explorer version 4 (with the introduction of Microsoft's Trident layout engine) and Netscape Navigator version 2 (along with the first JavaScript implementation), it has been possible to listen for DOM events in order to provide a more dynamic user experience in the browser. The first implementation of this model was quite limited, but as the browser evolved via standardization, so did the events system. Today, we have a fairly elegant native API for listening to and triggering both standardized DOM events _and_ custom events. You'll see throughout this chapter how you can make use of events in modern browsers to accomplish various tasks.

While the modern web does provide a powerful and intuitive set of methods for working with events, this was not always the case. That reality, along with lack of parity between Internet Explorer and the rest of the browsers in terms of the events API, made jQuery a great library for dealing with events cross-browser. In addition to normalizing event handling, jQuery also provides support for helpful techniques, such as event delegation and one-time event listener binding. While jQuery is no longer needed to normalize significant event API implementation differences between browsers, it still provides added convenience through extra useful features. In this chapter, I'll also show you how to use the most important event-related jQuery features simply by relying on the native web API.

In this chapter, I'll discuss how to create, trigger, and listen for standard browser events _and_ custom events. The jQuery method will be compared to the native web API approach, and you'll feel much more comfortable dealing with events yourself, without using jQuery as a crutch. At the very least, even if you decide to continue using jQuery for event handling in your projects, this chapter will give you a comprehensive understanding of the event system provided by the browser. While I will be focusing primarily on [modern browsers](#modern-browsers) in this chapter, a section at the end will include some useful information that will help you understand how the events API works in [ancient browsers](#ancient-browsers), and how this differs from the modern system.


## How do events work?

Before I cover _using_ events to solve common problems, I think it's prudent to first outline how browser events "work". The event system in this context does follow the publish-subscribe pattern at a basic level, but there is much more to browser events than this. For starters, there are multiple classifications of browsers events. The two broadest categories are referred to as "custom" and "native" (by me). "Native" browser events can be further assigned to sub-groups, which I will discuss shortly. Apart from event types, browser events also have a unique attribute - the methods in which they are dispersed to listeners. In fact, there are two distinct ways that a browser event can be broadcast across a page. Individual listeners can affect these events in various ways as well. In addition to event _types_, I'll also explain event _propagation_ in this section. After completing this first section, you will have a good understanding of the core concepts surrounding browser events. This will allow you to effectively follow the subsequent sections that outline specific use of the events API.


### Event types: Custom, and native

To start off my comprehensive coverage of browser events, I'll now introduce you to the two high-level categories that all events fit into: "custom", and "native". Native events are those defined in one of the official web specifications, such as those maintained by [WHATWG or W3C](#whatwg-vs-w3c). A listing of most events can be found in the [DOM Level 3 UI Events specification][dom3-ui-events-w3c], maintained by the W3C. Please note that this is _not_ an exhaustive list, it only contains a subset of the events available today. Some of the native events include "click", which is an event that is triggered by the browser when a DOM element is selected via a pointed device or keyboard. Another common event is "load", which is fired when an `<img>`, document in a `window`, or `<iframe>` has successfully loaded. There are quite a few different native events available. A good resource that provides a list of all currently available native events can be seen on the [Mozilla Developer Network events page][events-mdn].

Custom events are, as you might expect, non-standard events that are specific to a particular application or library. They can be created on-demand to support dynamic event-based workflows. For example, consider a file upload library that would like to fire an event whenever an file upload has started, and then another when the file upload is finished. Just after (or perhaps just before) the upload commences, the library might want to fire an "uploadStart" event, and then "uploadComplete" once the file is successfully on the server. There really aren't any native events that provide the semantics needed for this situation, so custom events are the best solution. Luckily, the DOM API does provide a way to trigger custom events. While triggering custom events has been a bit of a hassle cross-browser without a [polyfill](#shims-and-polyfills), that is slowly changing. More on that later.

There is an interesting limitation associated with jQuery when dealing with custom events, something you won't find anywhere in the jQuery documentation. A custom event created and triggered without jQuery can be observed and handled using jQuery's event API. However, a custom event created and triggered with jQuery's event API _cannot_ be observed and handled without also using jQuery's event API. In other words, custom events created by jQuery are entirely proprietary and non-standard. The reason for this is actually quite simple. While it is possible for jQuery to trigger all event handlers on a specific element for a native DOM event, it is _not_ possible to do the same for custom events, nor is it possible to query a specific HTML element for its attaches event listeners. For this reason, jQuery custom events are only usable by jQuery custom event handlers.

The one thing that ties custom and native events together is [the `Event` object][event-object-w3c], which is described in a specification curated by the W3C. Every DOM event, custom or native, is represented by an `Event` object, which itself has a number of properties and methods useful for identifying and controlling the event. For example, a `type` property makes the name of the custom or native event available. A "click" event has a `type` of 'click' on its corresponding `Event` object. That same `Event` object instance will also contain, among others, a `stopPropagation()` method, which can be called to prevent the click event from being further broadcasted to other listeners on the page. I'll cover event propagation in the next section.


### Event propagation: Bubbling vs capturing

In the early days of the web, Netscape provided one way to disperse events throughout the DOM - event capturing - while Internet Explorer provided a contrasting method - event bubbling. Before standardization, browsers essentially made their own siloed choices when implementing features, which is what led to these two divergent approaches. This all changed in 2000 when the [W3C](#whatwg-vs-w3c) drafted the [DOM Level 2 Events Specification][dom2-events]. This document described an event model that included both event capturing _and_ bubbling. All browsers that adhere to this specification follow this method of distributing events across the DOM. Currently, all [modern browsers](#modern-browsers) implement DOM Level 2 Events. [Ancient browsers](#ancient-browsers), which only support event bubbling, will be covered at the end of this chapter.   

In all modern browsers, per the DOM Level 2 Events spec, When a DOM event is created, the capturing phase begins. Assuming the event is not cancelled at some point after it is triggered, it starts at `document` and propagates downwards, starting with the top-most ancestor of the element that triggered the event (the target element), and ending with this target element. After the capturing phase is complete, the bubbling phase commences. Starting with the target element, the event "bubbles" up the DOM, hitting each ancestor, until the event is cancelled or until it hits `document`.

If the description regarding the progress of an event is still a bit confusing, let me try to explain with a simple demonstration. Consider the following HTML document:

{title="HTML element ID - markup", lang=html}
~~~~~~~
<!DOCTYPE html>
<html>
<head>
  <title>event propagation demo</title>
</head>
<body>
  <section>
    <h1>nested divs</h1>
    <div>one
      <div>child of one
        <div>child of child of one</div>
      </div>
    </div>
  </section>
</body>
</html>
~~~~~~~

Suppose the `<div>child of child of one</div>` element is clicked. The click event takes the following path through the DOM:

**Capturing Phase:**

1. `document`
2. '<body>'
3. `<section>`
4. `<div>one`
5. `<div>child of one`
6. `<div>child of child of one`

**Bubbling Phase:**

7. `<div>child of one`
8. `<div>one`
9. `<section>`
10. `<body>`
11. `document`

So, when it is appropriate to focus on the capturing phase over the bubbling phase, or vice versa? The most common choice, without a doubt, is to intercept events in the bubbling phase. One reason to focus on the bubbling phase is due to historical reasons. Before Internet Explorer 9, this was the _only_ phase available. This is no longer an obstacle with the advent of standardization and modern browsers. In addition to lack of support for capturing in ancient browsers, jQuery _also_ lacks support for this phase. This is perhaps yet another reason why it is not particularly popular and bubbling is the default choice. But there is no denying that the concept of event bubbling is more intuitive than capturing. Envisioning an event that moves up the DOM tree, starting with the element that created the event, seems to be a bit more sensible than an event that _ends_ with the event that created it. In fact, capturing is rarely discussed when describing the browser event model. Finally, attaching a handler to the event bubbling phase is the default behavior when using the web API to listen for events.

Event though the event bubbling phase is often preferred, there _are_ instances where capturing is the better choice. There appears to be a performance advantage to utilization of the capturing phase. Since event capturing occurs _before_ bubbling, this seems to make sense. Basecamp, a web-based project management application, [has made use of event capturing to improve performance in their project][basecamp-capturing], for example. Another reason to make use of the capturing phase: event delegation for ["focus"][focus-w3c] and ["blur"][blur-w3c] events. While these events do not bubble, handler delegation is possible by using the capturing phase. I'll cover [event delegation](#event-delegation) in detail later on in this chapter. Finally, capturing can be used to react to events that have been cancelled in the bubbling phase. There are a number of reasons to cancel an event, that is, to prevent it from reaching any subsequent event handlers. Almost always, events are cancelled in the bubbling phase. [I'll discuss this a bit more later in this chapter](#modifying-events), but for now imagine a click event cancelled by a third-party library in your web application. If you still need access to this event in another handler, you can register a handler on an element in the capturing phase.

jQuery has made the unfortunate choice of artificially bubbling events. In other words, when an event is triggered via the library, it calculates the expected bubbling path, and triggers handlers on each element in this path. jQuery does not make use of the native support for bubbling and capturing provided by the browser. While this certainly adds complexity and bloat to the library, it's not clear if there are any performance consequences.


## Creating and firing DOM events

To demonstrate firing DOM events with and without jQuery, let's use the following HTML fragment:

{title="DOM event triggering demo - markup", lang=html}
~~~~~~~
<div>
  <button type="button">do something</button>
</div>

<form method="POST" action="/user">
  <label>Enter user name:
    <input name="user">
  </label>
  <button type="submit">submit</button>
</form>
~~~~~~~


### jQuery

jQuery's events API contains two methods that allow DOM events to be created and propagated throughout the DOM: `trigger` and `triggerHandler`. The `trigger` method is most commonly used - it allows an event to be created and propagated to all ancestors of the originating element via bubbling. Remember that jQuery artificially bubbles all events, and it does _not_ support event capturing. The `triggerHandler` method differs from `trigger` in that it only executes event handlers on the element in which it is called - the event is _not_ bubbled to ancestor elements. Really, jQuery's `triggerHandler` is different from `trigger` in many other ways, but the definition I have provided is sufficient for this section.

Below, I'll use jQuery's `trigger` function to submit the form in two ways, focus the text input, and then _remove_ focus from the input element.

{title="triggering DOM events - jQuery", lang=javascript}
~~~~~~~
// submits the form
$('form').trigger('submit');

// submits the form by clicking the button
$('button[type="submit"]').trigger('click');

// focuses the text input
$('input').trigger('focus');

// removes focus from the text input
$('input').trigger('blur');
~~~~~~~

To be fair, there is a second way to trigger the same events with jQuery, by using aliases for these events defined in the library's API:

{title="triggering DOM events - jQuery", lang=javascript}
~~~~~~~
// submits the form
$('form').submit();

// submits the form by clicking the button
$('button[type="submit"]').click();

// focuses the text input
$('input').focus();

// removes focus from the text input
$('input').blur();
~~~~~~~

What if we want to click on the button that appears before the form, but we don't want to trigger any click handlers attached to ancestor elements. Suppose the parent `<div>` contains a click handler that we do not want to trigger in this instance. With jQuery, we can use `triggerHandler` to accomplish this:

{title="triggering DOM events without bubbling - jQuery", lang=javascript}
~~~~~~~
// clicks the first button - the click event does not bubble
$('button[type="button"]').triggerHandler('click');
~~~~~~~


### Web API

There are between two and three ways to trigger the same events demonstrated above _without_ using jQuery (depending) on the browser. It's good to have choices (sometimes). Anyway, the easiest way to trigger the above events using the web API is to invoke corresponding native methods on the target elements. This gives us code that looks very similar to the second block of jQuery code above.

{title="triggering DOM events - web API - all modern browsers + IE8", lang=javascript}
~~~~~~~
// submits the form
document.querySelector('form').submit();

// submits the form by clicking the button
document.querySelector('button[type="submit"]').click();

// focuses the text input
document.querySelector('input').focus();

// removes focus from the text input
document.querySelector('input').blur();
~~~~~~~

The above code is only limited to IE8 and newer due to use of `querySelector`, but this is far more than sufficient given the current year and state of browser support. The `click`, `focus`, and `blur` methods are available on all DOM element objects that inherit from [`HTMLElement`][htmlelement-html5]. The `submit` method is available to `<form>` elements only, as it is defined on the [`HTMLFormElement` interface][htmlform-html5]. The "click" and "submit" events bubble when they are triggered with these methods. Per [the W3C specification][dom2-events], "blur" and "focus" events do _not_ bubble, however. They are available to other event handlers in the capturing phase though.

The above events can _also_ be created by using either the `Event` constructor, or [the `createEvent` method available on `document`][createevent-document]. The former is supported in all modern browsers, with the exception of any version of Internet Explorer. In the next code demonstration, I'll show you how to programmatically determine if the `Event` constructor is supported, and then fall back to the alternative path for triggering events. Perhaps you are wondering why you would even need to trigger events using an approach different from the simple one outlined above. If you wish to alter the default behavior of an event in some way, constructing an `Event` object in some way is required. For example, in order to mimic the behavior of jQuery's `triggerHandler` method and prevent an event from bubbling, we must pass specific configuration to our `click` event when constructing it. You will see this at the end of the following code section that demonstrates a second approach to triggering events, which is needed to prevent our click event from bubbling up the DOM.

{title="triggering DOM events without bubbling - web API - all modern browsers", lang=javascript}
~~~~~~~
var clickEvent;

// If the `Event` constructor function is not supported,
// fall back to `createEvent` method.
if (typeof Event === 'function') {
  clickEvent = new Event('click', {bubbles: false});  
}
else {
    clickEvent = document.createEvent('Event');
    clickEvent.initEvent('click', false, true);
}

document.querySelector('button[type="button"]')
  .dispatchEvent(clickEvent);
~~~~~~~

Above, when we must fall back to `initEvent`, the second parameter is `bubbles`, which must be set to `false` when the event must _not_ bubble. The `Event` constructor provides a more elegant way to set this and other options - using a set of object properties. Once Internet Explorer 11 is dead and buried, we can focus exclusively on the `Event` constructor and forget `initEvent` ever existed. But until then, the above check will allow you to choose the proper path if you must construct and event with special configuration options.


## Creating and firing custom events

Remember that custom events are those that are not standardized as part of an accepted web specification, such as those maintained by [the W3C and the WHATWG](#whatwg-vs-w3c). Let's imagine a scenario where we are writing a third-party library that handles adding and removing items from a gallery of images. When our library is integrated into a larger application, we need to provide a simple way to notify any listeners when an item is added or removed by this library. In this case, our library will wrap the gallery of images, so we can signal a removal or addition simply by trigger an event that can be observed by an ancestor element. There aren't any standardized DOM events that are appropriate here, so we'll need to create our own event, a _custom_ event. The custom event associated with removing an image will be aptly called "image-removed", and will need to include the ID of the removed image.

### jQuery

Let's first fire this event using jQuery. We'll assume that we already have a handle on an element controlled by our library. Our event will be triggered by this particular element.

{title="triggering custom events - jQuery", lang=javascript}
~~~~~~~
// Triggers a custom "image-removed" element,
// which bubbles up to ancestor elements.
$libraryElement.trigger('image-removed', {id: 1});
~~~~~~~

This looks identical to the code used to trigger native DOM events, and it is. jQuery has a simple, elegant, and consistent API for triggering events of all types. But there is a problem here - the code that listens for this event, outside of our jQuery library, _must_ also use jQuery to observe this event. This is a limitation of jQuery's custom event system. It matters little if the user of our library is using some other library or even if the user does not wish to use jQuery outside of this library. Perhaps it is not clear to our user that jQuery _must_ be used to listen for this event. They are forced to rely on jQuery and jQuery alone to accept messages from our library.


### Web API

Triggering custom events with the web API is exactly like triggering native DOM events. The difference here is with _creating_ custom events, although the process and API is still quite similar.

{title="triggering custom events - web API - all modern browsers except Internet Explorer", lang=javascript}
~~~~~~~
var event = new CustomEvent('image-removed', {
  bubbles: true,
  detail: {id: 1}
});
libraryElement.dispatchEvent(event);
~~~~~~~

Above, we can easily create our custom event, trigger it, and ensure it bubbles up to ancestor elements. So, our "image-removed" event is observable outside of our library. We've also passed the image ID in the `detail` property of the event payload. More on accessing this data later on. But there's a problem here - this will _not_ work in any version of Internet Explorer. Unfortunately, as I mentioned in the last section, Explorer does not support the `Event` constructor. So, we must fall back to the following approach in IE:

{title="triggering custom events - web API - all modern browsers", lang=javascript}
~~~~~~~
var event = document.createEvent('CustomEvent');
event.initCustomEvent('image-removed', false, true, {id: 1});
libraryElement.dispatchEvent(event);
~~~~~~~

Instead of creating an 'Event', as we did when attempting to trigger a native DOM event in Internet Explorer in the previous section, we must instead create a 'CustomEvent'. This exposes an `initCustomEvent` method, which is defined on the `CustomEvent` interface. This special method allows us to pass custom data along with this event, such as the image ID in this case.

The above code currently (as of early 2016) works in all modern browsers, but this _may_ change in the future, since [`CustomEvent.initCustomEvent` has been deprecated in the W3C UI Events specification][customevent-w3c]. This means that is may be removed from any future browser version. To make our code future proof and still ensure it works in Internet Explorer, we will need to use the same check for the `CustomEvent` constructor from the previous section.

{title="triggering custom events - web API - all modern browsers", lang=javascript}
~~~~~~~
var event;

// If the `CustomEvent` constructor function is not supported,
// fall back to `createEvent` method.
if (typeof CustomEvent === 'function') {
  event = new CustomEvent('image-removed', {
    bubbles: true,
    detail: {id: 1}
  });  
}
else {
    event = document.createEvent('CustomEvent');
    event.initCustomEvent('image-removed', false, true,
      {id: 1}
    });
}

libraryElement.dispatchEvent(event);
~~~~~~~

After Internet Explorer fades into obsolesce and Microsoft Edge takes its place, the above code will no longer be needed at all.


## Listening (and un-listening) for event notifications
%% Why is this important?
%% How can we do this w/ and w/out jQuery?
%% w/out jQuery: inline, element properties (e.g. onclick), addEventListener, and non-standard attachEvent
%% one-time event listener binding w/ and w/out jQuery


## Modifying an event inside your event listener {#modifying-events}
%% preventDefault
%% stopPropagation
%% stopPropagationImmediate
%% attaching data for subsequent listener consumption


## Event delegation: powerful and underused {#event-delegation}
%% Penalty for too many events listeners?
%% Why else are delegated events important?
%% React has even built this in to their library.
%% Scenario where delegated event handling is very useful
%% jQuery vs web API handling


## Handling and triggering keyboard events
%% why is there a special section for this?


## Determining when something has loaded


## A history lesson: Ancient browser support
%% listening/handling/un-listening
%% forms
%% working with the Event object
%% load events


[basecamp-capturing]: https://signalvnoise.com/posts/3137-using-event-capturing-to-improve-basecamp-page-load-times
[blur-w3c]: https://www.w3.org/TR/uievents/#event-type-blur
[createevent-document]: https://www.w3.org/TR/DOM-Level-3-Events/#widl-DocumentEvent-createEvent
[customevent-w3c]: https://www.w3.org/TR/uievents/#idl-interface-CustomEvent-initializers
[dom2-events]: https://www.w3.org/TR/DOM-Level-2-Events/
[dom3-ui-events-w3c]: https://www.w3.org/TR/DOM-Level-3-Events
[events-mdn]: https://developer.mozilla.org/en-US/docs/Web/Events
[event-object-w3c]: https://www.w3.org/TR/uievents/#h-event-interfaces
[focus-w3c]: https://www.w3.org/TR/uievents/#event-type-focus
[htmlelement-html5]: https://www.w3.org/TR/html5/dom.html#htmlelement
[htmlform-html5]: https://www.w3.org/TR/html5/forms.html#the-form-element

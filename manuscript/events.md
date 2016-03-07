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
%% is one _better_ than the other? why?
%% explain and provide example scenarios for both
%% jquery artificially bubbles events, doesn't support capturing


## Creating and firing DOM events
%% 1 way to do this w/ jQuery
%% At least one way to do this w/out jQuery, depending on the event
%% Use the following events in an example: click, focus, blur, and submit


## Creating and firing custom events
%% Remember the limitation w/ using jQuery-created custom events
%% 2 ways to create custom events w/out jQuery
%% Example scenario


## Listening (and un-listening) for event notifications
%% Why is this important?
%% How can we do this w/ and w/out jQuery?
%% w/out jQuery: inline, element properties (e.g. onclick), addEventListener, and non-standard attachEvent
%% one-time event listener binding w/ and w/out jQuery


## Modifying an event inside your event listener
%% preventDefault
%% stopPropagation
%% stopPropagationImmediate
%% attaching data for subsequent listener consumption


## Event delegation: powerful and underused
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


[dom3-ui-events-w3c]: https://www.w3.org/TR/DOM-Level-3-Events
[events-mdn]: https://developer.mozilla.org/en-US/docs/Web/Events
[event-object-w3c]: https://www.w3.org/TR/uievents/#h-event-interfaces

# Browser Events (IN PROGRESS) {#browser-events}

Reacting to changes on a page is a big part of modern web application development. While this has always been possible to _some_ degree with anchor links and form submit buttons, the introduction of an events system made it possible to adjust to user input without reloading or changing the current page. You can probably see how this complements the ability to send [ajax requests](#ajax-requests) as well. Since Internet Explorer version 4 and Netscape Navigator version 2, it has been possible to listen for DOM events in order to provide a more dynamic user experience in the browser. The first implementation of this model was quite limited, but as the browser evolved via standardization, so did the events system. Today, we have a fairly elegant native API for listening to and triggering both standardized DOM events _and_ custom events. You'll see throughout this chapter how you can make use of events in modern browsers to accomplish various tasks.

While the modern web does provide a powerful and intuitive set of methods for working with events, this was not always the case. That reality, along with lack of parity between Internet Explorer and the rest of the browsers in terms of the events API, made jQuery a great library for dealing with events cross-browser. In addition to normalizing event handling, jQuery also provides support for helpful techniques, such as event delegation and one-time event listener binding. While jQuery is no longer needed to normalize significant event API implementation differences between browsers, it still provides added convenience through extra useful features. In this chapter, I'll also show you how to use the most important event-related jQuery features simply by relying on the native web API.

In this chapter, I'll discuss how to create, trigger, and listen for standard browser events _and_ custom events. The jQuery method will be compared to the native web API approach, and you'll feel much more comfortable dealing with events yourself, without using jQuery as a crutch. At the very least, even if you decide to continue using jQuery for event handling in your projects, this chapter will give you a comprehensive understanding of the event system provided by the browser. While I will be focusing primarily on [modern browsers](#modern-browsers) in this chapter, a section at the end will include some useful information that will help you understand how the events API work in [ancient browsers](#ancient-browsers), and how this differs from the modern system.


## How do events work?


### Event types: Custom, and native
%% custom vs. native
%% custom events created with jQuery are not visible outside of jQuery, also artificial bubbling


### Event propagation: Bubbling vs capturing
%% is one _better_ than the other? why?
%% explain and provide example scenarios for both


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

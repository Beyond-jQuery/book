# Browser Events (IN PROGRESS) {#browser-events}
%% What are events?
%% jQuery was important for event handling when IE8 was still commonly supported
%% Focus on modern browsers, will have a brief section on ancient browser support at the end


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

# Controlling Asynchronous Tasks (IN PROGRESS) {#async-tasks}

%% what is an async task
%% real async situations
%% APIs: the benefits of async support
%% jQuery is _way_ behind the web API in this area


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

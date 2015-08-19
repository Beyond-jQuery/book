# The Oppressive Magic of jQuery

For many years, both amateur and professional web developers alike have made use of jQuery to ease the burden of bringing a library or web application to market. In a sense, jQuery has been an integral part of web development. Even at the writing of this book, [jQuery is referenced in the vast majority of public websites][jquerymarketshare], more so than any other library, by a wide margin.

Many developers seem to consider jQuery to be a default requirement. The common thought is - if you are developing a library or a web application, you must depend on jQuery. jQuery is seen as this magical black box that solves all of the woes of web development. It's a framework that is easy to understand, and allows even novices to swiftly codify their ideas.

Professionals tend to be quite invested in jQuery as well. After all, this is what we used back when _we_ were novices. It's comfortable. We understand it. We trust it. It has served us well over the years. You don't have to think (much) about the DOM, or browser bugs, or cross-browser behaviors. jQuery solves all of these problems for us... doesn't it?

There is no denying that jQuery _is_ indeed a bit magical. In fact, it allows developers of almost any skill level to create something useful. But at what cost? Consider only understanding the web through rose-colored jQuery lenses. What if you encounter a low-level behavior that jQuery has not properly abstracted? What if you encounter a bug in jQuery? What if you are simply not able (allowed) to use jQuery? This is akin to a city dweller being dropped into the tundra of Siberia. Under these conditions, you are scared, disoriented, and ill-prepared.

While the possibilities of being whisked away to a foreign land against your will is unlikely, being without jQuery in the future is a much more realistic possibility. If you don't have a good grasp of the DOM, the web API, and JavaScript, you will eventually feel a _bit_ like a cold and confused urbanite, trying to survive the unfamiliar conditions of the Siberian expanse.

One goal of “Beyond jQuery” is to demystify this seemingly ubiquitous front-end library. The benefits of shedding any blind dependence on jQuery will become clear. At the end of this book, you will have the power to grow further as a web developer given your newfound understanding of the browser's API _and_ JavaScript.

In this chapter, we will explore reasons why developers have depended on jQuery, and why they continue to do so. You will see why relying entirely on one monolithic library creates gaps in your knowledge and prevents you from evolving as a developer. We will discuss why a filtered understanding of the web and JavaScript is a potentially hazardous predicament for developers. Given this knowledge, you will be able to better understand the benefits of depending more on your own solid understanding of the fundamentals.


## Why have we been using jQuery?

Before we explore how (and why) we should consider removing jQuery from our toolbox, we should first understand why jQuery even exists. Why have countless web developers over the years depended on this library? Why has it been such a central component of web sites and applications? Why does it continue to be so ubiquitous? Why _have_ we been using jQuery? We all have our reasons, and there are many of them indeed. Above all else, jQuery has proven to offer a low barrier to entry. In other words, even amateur or occasional developers find that it allows them to implement a concept or idea with little resistance.


### Simplicity

The assumption is that, if you are reading this book, you are already somewhat familiar with jQuery. You have likely already used it in some project, regardless of the size. So let's explore _why_ this library is so easy for developers of all skills levels to use.

Above all else, jQuery's API is intuitive. Want to add a CSS class to an element? Just use the `addClass()` method. Need to send a POST request? Just use the `post()` method. And hiding an element is as simple as passing it as a parameter into jQuery's `hide()` method.

The magic of jQuery is evident in its dirt-simple API. This is allows those without any prior knowledge of the browser or JavaScript to create something intriguing and useful. This is quite appealing and arguably even most appropriate for those who only dabble in web development. Conversely, the simplicity of jQuery is also a potential hazard for professional web developers. If you do not already believe this to be true, we will explore this theory a bit more later on.


### Community

On Stack Overflow (at the writing of this book) 800,000 questions are tagged as JavaScript questions, and 600,000 are tagged as _jQuery_ questions. jQuery is the 5th-most popular tag on Stack Overflow. The next most popular front end library is AngularJS in a distant 34th place, which only has 80,000 tagged questions. 300,000 questions are tagged as both jQuery and JavaScript on Stack Overflow, which means that 500,000 questions are tagged as jQuery and _not_ JavaScript. In many cases, jQuery is not seen as a JavaScript library. In fact, it is seen as an alternative to JavaScript. A way to address the browser without having to ever deal with the underlying language or API. While the relation to JavaScript may not be clear to some, jQuery has no shortage of developers willing and ready to offer advice on the library. Of the 600,000 jQuery-tagged questions on Stack Overflow, 530,000 (88%) contained at least one answer.

As of 2015, jQuery is the most used JavaScript library in public websites. In fact, [62% of all public websites depend on jQuery in some way][jquerymarketshare2]. The next most popular library is Modernizr, which is only used in 8% of all public sites. With this impressive market share, there are certainly a fair share of users with some working knowledge of the topic.

In addition to the jQuery tag on Stack Overflow and the wealth of different forums and sites dedicated to advising those invested in the technology, jQuery's site has its own active user forums. Help is easy to find, and any problem you may run into has likely already been solved and discussed at length. The reality of a large and mature community is an appealing reason to depend on any software library.


### Habit

The large number of examples, blog posts, and forums aimed at jQuery beginners is one of the reasons why those new to web development choose this library to assist with their project. But what about seasoned developers? Why do they _continue_ to use jQuery in _their_ projects? A polished, experienced developer was once an amateur. As amateurs, they may very well have embraced jQuery. Now, with multiple projects under their belt, jQuery has proven itself. And even if some of the flaws in the library have been noticed, they are well understood.

To a seasoned developer, jQuery is consistent and reliable enough. It has become part of the development process. A strong community is a benefit realized by experienced developers as well, and this is yet another reason to stick with such a trustworthy tool.

jQuery has been a prerequisite for everything we write. We mindlessly import it, partially because we have been trained, out of habit, to do so. We have been trained to think that this is a vital component of every web application. Habits are hard to break, especially those that have produced positive results.

Software development can be stressful and frustrating. A typical developer wrestles with a seemingly infinite number of variables on a daily basis. No problem, it seems, is simple to solve. Consistency and predictability of a tool, process, or outcome is highly desired and rare. Can you blame the web development community for relying on a consistent and reliable tool like jQuery for so long?


### Elegance

Have you ever heard anyone claim that the DOM is ugly or JavaScript is flawed and riddled with time bombs? Perhaps you think this yourself. While beauty is mostly subjective, this seems to be a surprisingly common thought, especially among more seasoned developers who continue to use jQuery late into their web development career.

The oft perceived lack of elegance in the native browser API and JavaScript ostensibly drives developers to this library, among other things. The thought is that simple problems are difficult to solve without the aid of jQuery. Don't believe me? Ask some of the developers you work with. Why do they use jQuery? Expect to hear about how simple it is to create elegant and terse code in response to common problems. As discussed earlier in this section, the API itself is intuitive and elegant. But the elegance of jQuery is more than simply a predictable API. The usability considerations surrounding the design of the API _further_ this claim of elegance.

Take method chaining for example, which allows a developer to very easily tie together a number of operations on the same element or elements without the burden of repetition or temporary variable creation. Suppose you want to select a set of elements, then add a class to all of them, and finally add a class to an even more specific subset of the initial set of elements. You can do _all_ of this very easily by harnessing the elegance of method chaining provided by jQuery's API. The following block of code demonstrates this by adding a class of "underline" to all elements that contain a class of "alphabet". It then selects only child elements of "alphabet" elements that contain a class of "vowels" and finally annotates them with a class of "bold" while hiding any child of "vowels" elements that themselves also contain a class of "a".

{title="jQuery method chaining", lang=javascript}
~~~~~~~
$('.alphabet').addClass('underline')
   .find('.vowels').addClass('bold')
      .find('.a').hide();
~~~~~~~

I have found that many developers tend to struggle with asynchronous operations in JavaScript, such as ajax requests. Making these requests is not difficult, but dealing with the result, given the asynchronous nature of the call, proves to be frustrating for some. jQuery simplifies this a bit by allowing a response handling function to be elegantly tied to the function call that sends the underlying request. The following code sends a request to retrieve a user's name, providing simple access to the result from the server's response.

{title="jQuery GET request", lang=javascript}
~~~~~~~
$.get('name/123', function(theName) {
   console.log(theName);
});
~~~~~~~

W> Note that the console object is not available in Internet Explorer 9 and older unless the developer tools are open. Also, the above example does not handle error responses, only success.

With all this "beauty", it's easy to forget about other attributes of our efforts, such as performance. Are there potential efficiency land mines in any of the above examples? Yes, but these may be tough to identify initially. We'll discuss this more in a later chapter.


### Fear

jQuery makes everything easier – web development is hard. You _can't_ develop a solid web application or library without some help. It's _far_ too difficult to ensure your app will work properly in all browsers without jQuery. The web API implementation varies wildly between browsers. All the good plug-ins you need depend on jQuery anyway. These are all common excuses for blindly depending on jQuery, and they are all based on fear. We have all relied on jQuery due to fear of the unknown. We see the DOM as a mysterious and unpredictable black box, littered with serious bugs. We fear cross-browser implementation variances.

The creator of jQuery, John Resig, famously concluded that ["The DOM is a mess"][domisamess] back in 2009. At this moment in web history, Internet Explorer 6 and 7 accounted for [almost 60% of the browser market][2009ie6marketshare]. With this in mind, it's hard to argue with Mr. Resig's statement at the time. The DOM was indeed a scary and fickle beast, and the most popular browsers of the day had very poor and limited built-in tools. What if we look even further back, to August of 2006 when jQuery was created and first released? At this time, the newest version of Internet Explorer was version 6. IE6 (and older) accounted for an incredible [83% of all browsers in use][2006ie6marketshare]. At this point, the Web API was exceptionally immature, stability of browsers was much lower than we have come to expect in the current era, and standards compliance was quite inconsistent across the browsers of the time.

In addition to immature developer tools, varying Web API implementations, and the unintuitive DOM, browsers can certainly be buggy. Similar to any other complex bundle of code, browsers are not immune from bugs. jQuery has historically [promised a wide range of browser bug workarounds][jquerybrowserfixes]. For many years, the web seemed to be akin to the wild west in terms of standards observance and quality control. It's not hard to see why a library that aims to normalize the browser landscape was so popular. No need to fear cross-browser support anymore. No need to even worry about cross-browser testing. jQuery will take do all of the heavy lifting for you, so you can focus entirely on developing intriguing and useful web applications and libraries. Or will it? While the hope is that jQuery will free you from all of the problems and complexities baked into the browser, the reality is a bit different.


## A crutch is only temporary

JavaScript libraries are often useful tools. They assist in your efforts to architect a useful and reliable web application, or perhaps even _another_ library. They save you time and keystrokes. They serve as a buffer between you, your code, and the browser, filling in the gaps and normalizing behavior. In another sense, these libraries can function as crutches. They help inexperienced and uneducated developers, without actually teaching them anything about the underlying complexity. Even though the tone of this book may at times suggest otherwise, libraries like jQuery are not inherently bad. They are only limiting if your learning does not progress beyond the library.

Does jQuery _always_ save you time? Is it _always_ making your web development experience easier? Are the conventions associated with the core library or its plugins intuitive? Does it really solve all of your problems, or does it perhaps create some new ones? Have you taken the time to ponder the syntax and conventions that come with the library? Is there a better solution, or does jQuery really patch all of the usability holes that developers often fall into? Mostly, jQuery's API is pleasing to the eye and remarkably intuitive. But of course this is not true _all_ of the time. There are certainly parts of the library that are unpleasant. Let us example the elegance and necessity of jQuery a bit.

jQuery does _not_ completely shield you from the browser's quirks. This is not a realistic goal of any library. Aside from this, jQuery is simple a library, a tool, an aid. It isn't meant to replace the _entire browser stack_. Some problems are even best solved with CSS or static HTML. But to the developer using jQuery as a crutch, it is the _only_ way to interface with the browser. To the uninformed developer, it is perfectly reasonable to write minimal HTML and make any and all adjustments to markup using jQuery's API. Or perhaps it would be even easier to generate all markup using jQuery. You can create elements with jQuery, and then easily insert them onto the page.

Instead of declaring styles in CSS files, the inclination is to use `$(element).css('fontWeight', 'bold')`. While very convenient, this is a horribly unmaintainable method of generating inline styles. The importance of separation of concerns may not be readily apparent to the new developer. The magical all-encompassing API of jQuery makes it easy to ignore the native tools available to us. The appropriate roles of HTML, CSS, and JavaScript do not always enter into the equation when you are blindly depending on a monolithic abstraction. This library is not just a tool for some, it is _the_ tool. It is the be-all and end-all of web development. You'll see why this is a dangerous line of thinking, especially for professional and aspiring developers.

Indeed, jQuery is a crutch for many. It is not fused to the browser and merely acts as a supplement. Experienced and knowledgeable developers may actually prefer to use jQuery, and there is certainly nothing wrong with that. But for others, it serves as a prop. Those new to the world of web development often pick up this crutch and hobble along for quite some time. But eventually, the crutch is pulled out from under them, and they fall.


### You are a mechanic, not a driver

A popular question on Stack Overflow asks ["Is it a good idea to learn JavaScript before learning jQuery?"][learnjqueryquestion] One particular answer to this question provides some curious advise. The contributor goes on to say in his answer ["You really don't need to focus too much on learning the ins and outs of the HTML DOM if you are going to use a framework like jQuery. Just do things the 'jQuery way' and then pick up as much HTML DOM as is necessary along the way."][learnjqueryanswer] Though this line of thinking is apparently _not_ common among others who contributed answers to this question (perhaps more experienced developers). I myself was a new and inexperienced web developer at one point, and remember how this train of thought was embraced by those entering the confusing world of browser-based front-end coding.

In a series of blog posts I wrote entitled ["You Don't Need jQuery"][ydnjq], a commenter provided a striking analogy that outlined one of the goals of the blog (and this book).

> "One of the things I try to guide my peers on is the fact that you don't put up walls on a foundation before you pour the foundation. In fact you should level the ground, lie the plumbing for functionality (testing) and then pour the foundation before you begin building the structure. This comes into understanding the core of your tools (HTML, CSS, JS) when building for any endpoint (browsers)"[^myblogcommentquote].

In other words, for a stable, long-lasting application or library, you must have a good understanding of exactly how your tools work. Short of this, you are not a developer. You are, in fact, a _library integrator_.

It's difficult to argue with the sound advice of the commenter above, but the stunning magic of jQuery seems to blind us sometimes. This does not make us "bad" developers. In fact, it may not even be our fault. We are wired to take the path of least resistance. This is actually a well studied and documented psychological theory, known as the "Principle of least effort"[^principleofleasteffort].

Playing devil's advocate, perhaps we could refute previously quoted analogy with another. Most of us probably drive a car on a daily basis. But how many of us are able to diagnose an issue with a typical internal combustion engine? How many of are even capable of anything beyond changing a flat tire? The answer to these questions is likely "very few".

Do we really need to be competent auto mechanics to drive a car? No, of course not. For many of us, driving is not a profession. Instead, it's a convenience that we rely on. We don't have the time to understand every minute detail of our cars, and we shouldn't have to. Cars exist to simplify our lives, to save us time. Car manufacturers don't expect their customers to be auto mechanics. They design their products with the average person in mind in order to ensure their cars are usable by as many people as possible, for obvious reasons.

Can we compare driving a car to driving the browser? As software developers, do we really need to understand the underpinnings of the web? As you saw in the above Stack Overflow answer, some may say "no". But it's important to understand that we are _not_ drivers. We are mechanics and designers, _not_ users.


### Stunted growth

What happens when you are thrust onto a new project without jQuery, without your crutch? If your capabilities stop at the edge of the library's API, your options are limited. You're very much invested in this abstraction, jeopardizing your ability to grow beyond it. Do you really want to be dependent on a single chunk of code for all of your projects? In the short term, this doesn't seem like a problem. Looking forward, the feasibility of this path becomes questionable.

The landscape of software development, and technology in general, is constantly changing. As developers, we not only understand this, we embrace it. This makes our jobs both challenging and interesting. There's nothing wrong with using jQuery, or any other library. But by using these as a crutch, we cease to be software developers. We're jQuery programmers.

As a new developer, your goals shouldn't necessarily revolve around agility. Learning the fundamentals at this early stage is paramount. By becoming comfortable with your environment – the browser – you put yourself in a better position to make good decisions as your project and career evolves. Only after you have a firm grasp of the fundamentals and a better understanding of web development in its most basic form should you focus on choosing and learning a tool to speed up your development process.

This advice is really not specific to software development. Do you remember back when you were first learning math? All of the exercises you completed could have easily be solved using a calculator. Most likely, you were strictly forbidden from using a calculator though (I know I was). Why? Calculators are faster and more accurate. Simply put, in this stage, the aim is not speed. A thorough understanding of the fundamentals – of math – is most important. Once you understand _how_ the calculator performs these tasks, you can choose to use one – or not – as you solve more complex problems in the future. Understanding the fundamentals ensures you are not chained to a tool.

When our library of choice fades into obsolescence or is otherwise pulled out from under us, our blind reliance prevents us from moving forward. Examples of this unfortunate situation are readily available. Take [a fairly recent article in JavaWorld][stupidjqueryarticle], for example. The author cites "6 reasons you should by using jQuery". The reasons are questionable as the author clearly is missing even a basic understanding of the browser stack. This is particularly evident in claims such as "jQuery is a major component of HTML5", conflating JavaScript with a document markup specification. Another troubling quote from this article: "jQuery pages load faster". It's this type of oversimplification that leads us, as developers, to take the complexity of our jobs for granted. By pretending that a beast like the browser can be tamed with a single library just opens us up to a frustrating struggle that we will eventually lose.


## The price of shortcuts (a true story)

What follows is a real story of a real web developer who took real shortcuts (really). He only focused on the short term, on making his job easier. His main concern was pleasing project managers. Learning the fundamentals was a waste of time to him. Churning out code and plowing through a long list of features as quickly as possible was his goal. That developer _was_ me.

jQuery makes everything easier. You can't develop a solid web app without it. It's far too difficult to ensure your app will work properly in all browsers without jQuery. The DOM API implementation varies wildly between browsers. All the good plug-ins you need depend on jQuery anyway. I bought into all of these excuses, and more. Some of them were even good excuses at one time.


### A new direction, a new web developer

Back in the very early days of my web development career, I was transitioning from exclusive server-side work. I was assigned to "Jennings", a web-based journalist production tool. I had no professional HTML, CSS, or JavaScript experience. My front-end skills were lacking, to say the least.

No one on the team was comfortable with web development. We were all rookies, former back-end developers struggling in vain to make sense of our knowledge in this new context. The deadlines were strict, the goals lofty. It seemed that we all needed some help. Perhaps a tool to make our jobs a bit easier.  There was no time to learn. We had an application to write!


### Shortcuts and my own stunted growth

My first exposure to JavaScript and the web was filtered through jQuery. In fact, I didn't even bother to learn proper JavaScript. I didn't have a clue what the web API looked like, or how to deal with the DOM directly. jQuery did everything for me. This huge gap in my knowledge caught up with me when I later ended up on a project without my jQuery crutch. I was forced to learn proper web development, and I never looked back.

After "Jennings" jQuery was a requirement (for me) in all future projects. It was a must, because I didn't know any other way to tame the browser. This didn't seem unusual at the time. In fact, it wasn't. jQuery was an expected dependency in most applications and libraries. My blind faith was not an obvious hinderance.

Some of the issues of this blind dependency became apparent, to some degree, as I searched for plug-ins to solve common problems in my projects. jQuery is a useful library by itself, but it only address core low-level concerns. If you want to support higher-level features, such as a modal dialog, you'll need to either write this yourself or find a plug-in that has already solved the problem for you.

Naturally, I looked _exclusively_ for jQuery plug-ins to fill the holes in my projects. In fact, I shied away from anything that didn't depend on it. I didn't trust any plug-in that _didn't_ make use of this wonderful magic box. jQuery solved all of my problems and made cross-browser development easy for me. Why should I trust the work of a developer who hadn't achieved this same level of enlightenment?

After a short while, it became apparent that the quality of _many_ of these jQuery plug-ins was surprisingly low.  The reality is, the low barrier to entry of jQuery is a double-edged sword. It's easy to quickly write sometime useful. But it's also just as easy to write maintainable bug-prone spaghetti code, quickly! I found that a lot of these plug-ins were very poorly written. My novice knowledge of web development made it tough to sort through and work around the issues I encountered with these jQuery plug-in libraries. Frustration set in, and cracks in my foundation as a developer began to appear.

But bugs and inefficiencies in poorly-written libraries only exposed the tip of the iceberg. Leaky abstractions in these plug-ins and even jQuery core were rampant and nearly impossible for me to make sense of. Why can't I trigger a custom event handler created outside of jQuery with jQuery? jQuery supports custom events, why doesn't this work? This was a specific issue I ran into when working on a project that relied both on jQuery and Prototype – an alternate JavaScript web framework with similar goals. I naively thought I could easily trigger event handlers bound with Prototype using jQuery – no such luck.

Take file uploading as another example. One would think that uploading files using jQuery is as simple as including the file as the `data` in the request. Not so – if you do this, jQuery will attempt to URL-encode the file. After a frustrating amount of reading and experimentation, I learned that two obscure properties must be set to `false` to ensure jQuery does not attempt to modify the file before the request is sent.

Yet another issue developers run into when blindly relying on this library – sending cross-domain requests in older browsers is nearly impossible with jQuery. This is a surprising realization to make when working with a library that is supposed to iron out Web API differences and allow older browsers to be managed with ease. We'll discuss all of this much more in [the chapter on ajax requests](#ajax-requests).

jQuery's attribute handling utility function changed in drastic ways over the course of the library's life. Let's consider a common task as an example – determining the state of a checkbox. In older versions of jQuery, the correct approach via jQuery was to make use of the `attr` method. If the checkbox is checked, a simple invocation of `$(checkboxEl).attr('checked')` would return `true`. Otherwise, it would return `false`. For a seasoned JavaScript developer, this is an odd behavior in itself, but we'll save those details for [the attributes chapter](#element-attributes).

For a narrowly focused jQuery developer, the story of this portion of jQuery's API gets worse. In later versions of jQuery, the same call would return the value of the checkbox element's "checked" attribute (which does not naturally change as the checkbox is checked and unchecked). While this is actually the correct behavior as it properly mirrors the element's actual attribute, it was confusing for me after the breaking change. I didn't have a proper grasp of HTML due to my over-reliance on jQuery. I didn't understand why I later had to rely on jQuery's `prop()` method to obtain the current state of the checkbox, even though the old behavior or the `attr()` method was technically incorrect.

I fell into a trap, and this is a trap that many new, occasional, and hobbyist web developers fall into. Had I taken the time to understand JavaScript and the API provided by the browser first, I would have saved myself a lot of trouble. The proper sequence of events is:

1. Learn JavaScript.
2. Learn the browser's API.
3. Learn jQuery (or any other framework/library that you may need across projects).

Many start with #3, delaying #1 & #2 to a much later date (or _never_). If you don't understand what jQuery is actually doing for you, there will be many frustrating days ahead as the leaky abstractions come out of the woodwork. This is a trap you must avoid if you want to effectively grow as a web developer, a trap that stunted my career as a web developer for longer than I would have liked.


### A challenge – no jQuery allowed!

In early 2012, I began to replace the Java Applet uploader in [Media Collective][collective], Widen's flagship digital asset management SaaS offering. Dealing with Java in the browser turned out to be a nightmare, and we were eager to move to a native JavaScript/HTML solution. I first looked into [jQuery File Upload][jqueryfileupload] (the most popular upload library at the time), but was put off by the large number of required dependencies needed to get this up and running as well as the lack of documentation. So, for one of the first times in my web development career, I settled on a non-jQuery solution, and I was a bit lost at first.

The library I decided to replace our Java applet uploader with was, at the time, called valums/file-uploader (due to its location on GitHub). It was unique in the respect that it was completely dependency free. I was a bit skeptical at first as I was trained to put a lot of faith in the jQuery ecosystem, but was pleasantly surprised at the ease at which I was able to integrate the plug-in.

However, the plug-in had fallen into disrepair. It was no longer actively maintained, and needed a few bugs addressed and features tweaked in order to make it production-ready for Media Collective. While the work required was not substantial, it took a significant amount of time me for to address these issues due to the large gaps in my knowledge of JavaScript, HTML, and CSS. I pushed some of my changes back up to a forked GitHub repository. [My code was sloppy and flawed, but it was sufficient][initialfineuploadercommits].

My efforts were apparently noticed by the creator of the library, Andrew Valums, who asked if I was interested in maintaining the library. Even though I had very little practical experience outside of jQuery, I jumped at the opportunity and accepted. I was now the sole maintainer of a large and very popular non-jQuery plug-in, to be renamed "Fine Uploader".

When I took over maintenance and development of Fine Uploader, a large cross-browser file upload library in mid-2012, my first instinct was to rewrite it all using jQuery, because that would make my life easier (I thought). [The existing user community was very much against bringing any 3rd-party dependencies into the library][fineuploaderjqueryissue], so I was forced to deal with the native web API and vanilla JavaScript instead.

My inexperience certainly slowed the evolution of Fine Uploader initially. I was forced to gain an expert-level understanding of the core concepts. I wrote my own small shims to account for cross-browser differences in the Web API and JavaScript. A good deal of my time was spent reading and experimenting. Over time, I was able to successfully shed my blind dependence on the oppressive magic of jQuery. I didn't need jQuery, and neither do you.


## Focus on the implementation, not the magic

The magic of jQuery and its promise of easy web application development is alluring. But we've discussed how and why you can become a stronger developer by understanding your environment first. Learn your trade by following the proper route: JavaScript, HTML, CSS, and the Web API first. Worry about libraries later.

Let me be frank with you, and admit that it is truly both interesting and surreal for a former clueless rookie web developer to now be playing the part of the wise “I’ve seen everything” developer. But I can say, with a high degree of confidence, that you are in a much better position to decide when you need to use jQuery, and when you don't, if you are more familiar with the fundamentals of web development. Knowledge and experience give you the freedom to make this choice and justify it with facts. You aren't permanently attached to any library. You have _options_.

Don’t hide behind your tools, own your code.  Become a web developer and a teacher, not a jQuery developer and a library user. There is something liberating about saying “I don’t need jQuery anymore. I can do it myself!” and actually meaning it. Don't get into the habit of taking shortcuts. Start out on a trajectory that you can be proud of as a professional. Don't put off learning the fundamentals until later, because later never happens. Avoid helplessness when your library of choice fails to shield you from the browser. You cannot realistically expect to hide behind a layer of abstractions for your entire career. The fundamentals are building blocks that will propel you further and empower you to master your trade.


[^myblogcommentquote]:  http://blog.garstasio.com/you-dont-need-jquery/why-not/#comment-1799026169. Lawrence Francell

[^principleofleasteffort]: George Kingsley Zipf (1949), Human behavior and the principle of least effort, Addison-Wesley Press

[2006ie6marketshare]: http://www.onestat.com/html/aboutus_pressbox44-mozilla-firefox-has-slightly-increased.html

[2009ie6marketshare]: http://www.w3counter.com/globalstats.php?year=2009&month=1

[collective]: http://www.widen.com/digital-asset-management-software/

[domisamess]: http://ejohn.org/blog/the-dom-is-a-mess/

[fineuploaderjqueryissue]: https://github.com/FineUploader/fine-uploader/issues/326

[initialfineuploadercommits]: https://github.com/FineUploader/fine-uploader/compare/82c8d5b0c383738ed84c771e90dbf202bd3acd68...55b3ca6e9f7a18fd3adc5ba7537124ae12b63e71

[jquerybrowserfixes]: https://docs.google.com/document/d/1LPaPA30bLUB_publLIMF0RlhdnPx_ePXm7oW02iiT6o/preview?sle=true#heading=h.fumxprdxo2gn

[jqueryfileupload]: https://github.com/blueimp/jQuery-File-Upload

[jquerymarketshare]: http://w3techs.com/technologies/history_overview/javascript_library/all/y

[jquerymarketshare2]: http://w3techs.com/technologies/history_overview/javascript_library/all/y

[learnjqueryanswer]: http://stackoverflow.com/a/841292/486979

[learnjqueryquestion]: http://stackoverflow.com/questions/668642/is-it-a-good-idea-to-learn-javascript-before-learning-jquery

[stupidjqueryarticle]: http://www.javaworld.com/article/2078613/java-web-development/6-reasons-you-should-be-using-jquery.html

[ydnjq]: http://blog.garstasio.com/you-dont-need-jquery/

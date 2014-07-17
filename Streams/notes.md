Title
---

* hi!
* my name is Gregor Elke, i'm from Hamburg
* thank you for having me here
* today i gonna talk about Streams
* especially in node.js, but you can use them in browsers too

* if you have questions please ask right away
---
* i'm greelgorke on the internet
* i love to work on and talk about Node.js, Streams and Software Architecture
---
* i'm the organizer of the node.js user group in Hamburg
---
* i'm working at codecentric
* and that's cool, because there i can do all the stuff full time
* and you can join me.
---
Agenda
---
* first we need to understand what a stream is
* then we'll see how they can be used
* at last I'll show you 3 libraries I know about and find useful
---

* but first let me ask you
* have you worked with streams already?
* what is a stream? any metaphor?

---
* my first metaphor was a river. obviously this is a stream right?
* this is Elbe in lower Saxony
---
* a stream as we imagine it in JS is
  * a sequence of data distributed in time
  * and potentially infinite
---
* or i'm used to think: sequence of messages
* we can't represent something distributed in time properly, time is to abstract.
* but with an indirection:
---
* we need a producer, or we say a 'readable'
* this is the spring of elbe
---
* then, we need a consumer, or 'writable'
* this is the aperture of elbe
---
* and the river in between we would call a 'pipe'
* so we represent streams as producers and consumers and use pipes between them
* as we have producers and consumers, we need to solve typical problems:
* backpressure and buffering
* and the tools handle this differently, but luckily they do
---
* let me show a few metaphors
---
* this is a pull-based readable
---
* this is a push-based readable with pause/resume function
---
* this is a typical writable
* ever piped something to /dev/null
---
* another pipe example
---
* what is this?
* what is 'data' here?
* this a transform stream: elevation energy to electric energy
* transforms are important! we'll see in a moment, why.
---
* how can we use streams?
* typically for memory usage control
* on the fly transformation of data
* unified interface! (think about unix pipes for the moment)
* flow control
* examples!!

------
Memory Control
---
* here is how you can consume a readable in Node, a file stream in this case
* wait for 'readable' message, then pull the data as you want
* the 'end'-event would mark EOF, but not shown here
* another, often better way to consume is to pipe the readable to a writable
---
On-The-Fly Transforms
---
* It is a simple Transform stream
* a Duplex Stream is a both: readable and writeable
* Transform is a special-case Duplex, where output depends on the input
* this one removes all vocals from the input-data
* use them in pipe chains
* here if the request has "Hamburg" as data, "Hmbrg" would land in the response
---
Module Interface
---
* In node ideally we create one-function modules.
* Extend this paradigm to one-stream-module
* export a stream, a stream constructor or an init function with a stream as result
* in this example the stream constructor is exported
* btw. this is the official way to implement a core stream. later more
---
Flow Control
---
* Streams can remove the need for flow control tools
  * here i use highland.js, because it's better suited for flow control
  * you don't need to understand it entirely, yet
* here is the idea: a http server is basically a stream of req-res-pairs
* so let's make it one
* every emitter of events can be wrapped in a stream
---
* what happens here:
* with fork() we create different branches with shared backpressure control
* in this branch we want to deal with POST only so we filter for them
* we do our thing with the request and
  * push only the res object further
* filter and map functions produce new streams from the old ones
* in each we terminate the http request by sending out a 201
---
* the second branch is simpler, but same game as before
  * fork and filter for GET
  * and send out a file for each url
* this is a simple file server
* you see transforms are everywhere, they are the cool ones
* now let's look at the tools, starting with
---
Node.js Core Streams (version 3)
---
* idea here is: Take the Unix Pipes as example
  * create a unified interface + format
  * define an event-based protokoll (data, end, readable, drain)
  * and wrap it in a handy method (pipe)
  * Node streams are special event emitters with the pipe method
* there are 4 abstract blueprints, which implement the hard low-level stuff
  * such as handling of backpressure, buffering etc.
  * so we can concentrate on our stuff.
* Readable comes with 2 modes (Stream 3), basically push and pull, paused by default
* primarily streams were meant for binary data, object mode makes them true message streams
* On npm you can find this api as module called "readable-stream", which can be used in browser
---
* we've seen a transform already, here is a simple Duplex example
* inherit, call the super constructor
* implement *_write* and *_read*
* ._write, ._read are called by internals on demand
---
* Simple example for browser
* wrapping a jQuery element into a Readable stream of characters
* nothing to do in *_read* here  
---
EventStream
---
* created by Dominic Tarr
* created with Streams1 API (node 0.8) in mind as a better option
* basic idea: custom streams should be
  * as easy to create as functions and
  * as easy to manipulate as arrays
* it's the mindset that streams are like arrays and streams are flow control tool
* the important thing: the main building block of this lib is through, a transform
* Event-Stream brings many ready-to-use transforms with it, more on npm
* examples!
---
* remember devocalize stream in core?
* it's practically a one-liner now
* even more compact with the replace stream
---
* this is how you implement a stream with through
* though-streams are push based, with pause/resume
* now we're getting functional
---
Highland.js
---
* made by Caolan McMahon, creator of async, nodeunit and member of the hoodie team
  * with flow control at first in mind, it combines functional programming paradigm with streams
  * highland want to be a replacement for tools like async and underscore
* Highland streams are pull-based and lazy evaluated
  * that means: nothing happens in the stream chain until a terminator functions is called
  * terminators are so called thunk-generators (each, toArray, pipe, collect, resume)
* you can curry and partially apply highland functions
* you can apply pattern matching on streams
* highland handles higher order streams, streams of streams
* highland maintians compatibility with node core streams
* examples!
---
* a simple http server, that receives and saves log entries
* in the example before we've seen forking and filtering on HTTP-methods
* here we fork and split the stream to substreams with pluck
* to get a stream of req objets, which are also streams
* the sequence method flattens the streams, the next pipe puts data from each req to stdout
* second fork is just for making http happy
---
* here you can see a counter stream created from a function
* anything special here? not really? there is this push(error)
* highland streams propagate errors through the pipe
  * no extra channel, but you can use .errors() and .stopOnError() functions to extract them
  * if errors are not handled and hit a thunk-method, then they will be thrown
---
* with last example i want to show you pattern matching and currying
  * almost every function in highland is curry-able
  * curryied stream has a transform function but no source, which is added later
* also you can see, that you can create streams from arrays (it's underscore replacement, right?)
* .where() filters values on a pattern passed to it
* This is Highland.js. There are many more functions and features in the lib, check it out
* here is my last advice:
---
Streams are awesome!
---
Thank you for your attention!
Questions?

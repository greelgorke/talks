Title
---

* hi and welcome
* my name is Gregor Elke
* i'm glad to be here and talk about one of my favorite topics: Streams.
* especially in node.js, also applies in browsers

* if i'm too quite or lost you, raise your hand
---
* i'm greelgorke on the internet
* love to work on and talk about
* working at codecentric
* awesome because they help me to be here
---
Agenda
---
* I want to talk about what is a stream in general
* about how they can be used
* and at last I show you 3 libraries I know about and find useful

* but first
* __ask__: who knows or have used streams? may be in other languages?
* __ask__: what is a stream?
* __go through cloud__
---

Stream is
---
* abstraction of sequence of data distributed in time
  * which is potentially infinite
  * an array on the other side is data in space/memory
* when we think about streams we have to ask
  * can we pull data out of it or will the stream push data at us, which implies different implementations of consumer code
  * do we have to deal with backpressure, fast producers and slow cosumers?
* because streams are really producers, consumers or both which
  * are often glued, or we say piped, together. pipe allows chaining
* mostly we think "binary data", when someone say stream
---
* but in javascript a stream is a sequence of messages or events
  * a message can hold any type of value
* this is why js streams are versatile

---
Versatile Streams
---
* you can use streams for
* memory control, which is a most common use case
* just in time transformations allow you to work on parts of the data as they come
* you can use stream as interfaces to your modules and create very composable systems
* at last streams are flow control tools, of course, we don't call them streams for nothing
* let's see some examples
  * you don't have to understand all the details here and now. we can talk about the examples afterwards
---
Memory Control
---
* here is how a stream can be consumed in Node
* typical case of reading and processing a file
* pull-based, wait for 'readable' event, decide when and how much to read
* here we read till the internal buffer is empty and process it. then we wait for the next event
* the 'end'-event would mark EOF, but not shown here
* another way to consume is to pipe it to a writable stream
---
JIT-Transforms
---
* It is a simple Transform stream
* Transform is a special-case Duplex, where output depends on the input
* typically use it in a pipe chain
* this one removes all vocals from the input-data
* so if the request has "Hamburg" as data, Devocalise would push "Hmbrg" to the response
---
Module Interface
---
* In node ideally we create one-function modules, extend this paradigm to one-stream-module
* export a stream, a stream constructor or an init function which returns a stream
* this case shows a stream constructor
* btw. second way of implementing a core stream. later more
---
Flow Control
---
* use streams for flow control
  * here i use highland, because it's better suited for flow control
  * you don't need to understand it entirely
* a http server could be a stream of req-res-pairs
* let's make it one
* every emitter of events can be wrapped in a stream
---
* what happens here:
* with fork() we create different branches of the stream which shares backpressure control
* in this branch we want to handle with POST only
* we do our thing with the request and
  * push the res object further
* filter and map produce new streams from the old ones
* in each we terminate the http request by sending out a 201
---
* now to the second branch
* this is simpler, same game as before
  * fork another branch, filter it for GET
  * and send out a file for each url
* a simple file server
* we will see more, when i talk about highland
* now let's look at the tools, starting with
---
Core
---
* idea here is:
  * take sequence of messages
  * define an event-based protokoll (data, end, readable, drain)
  * and implement it in a handy method
  * so Streams are special EventEmitters with a .pipe() method
* there are 4 abstract blueprints, which implement the hard low-level stuff
  * such as handling of backpressure, buffering etc.
  * so we as implementor can concentrate on on our stuff, by implementing 1-2 simple(r) methods
* Readable comes with 2 modes (Stream 3), push/pull, paused by default
* primarily streams were meant for binary data, object mode makes them true message streams
* On npm you can find "readable-stream" module, wich is a mirror of the core api. use with browserify & co
---
* Simple Duplex example
* Constructor Call
* write + cb
* read + push
* ._write, ._read called by internals on demand
---
* Simple example for browser
* wrapping a jQuery element into a Readable stream of characters
* nothing to do in _read, push only stream
* pausing may be implemented by buffering or dropping events
---
EventStream
---
* created by Dominic Tarr
* created with Streams1 API (node 0.8) in mind as a better api
* basic idea: custom streams should be
  * as easy to create as functions and
  * as easy to manipulate as arrays
* it's the mindset that streams are like arrays and streams are flow control tool
* Event stream brings many ready-to-use transforms with it
* also tools for merge-ing, split-ing and join-ing
* but most important: through and map as core
---
* remember devocalize stream in core?
* it's practically a one-liner now
* even more compact with .replace
---
* here is an example of .through implementation
* generic transform stream.
* though-streams are push based, but can be paused and resumed
* many other through-based streams on npm
---
Highland.js
---
* now it's getting functional
* made by Caolan McMahon, async, nodeunit, he's a hoodie fellow
  * highland want to be a replacement for async and underscore
  * flow control at first in mind, combining functional paradigm with streams
* pull-based and lazy evaluated
  * that means: nothing happens in the stream chain until a terminator functions is called
  * terminators are so called thunk-generators (each, toArray, pipe, collect, resume)
* you can curry and partially apply highland functions
* we've seen fork and map before
* you can apply pattern matching on streams
* highland handles higher order streams, streams of streams
* compatible with node core streams: pipe, pause, resume
---
* a simple log receiver, http server
* we've seen forking and filtering on HTTP-methods
* here fork and split the stream to substreams with pluck
* higher order stream: stream of req
* sequence flattens, the next pipe puts data from each req to stdout
* second fork is just for making http happy
---
* counter stream created from a function
* you can push values with the push-function, with next you signal that your async process is done
* error propagation through highland in the pipe
  * no extra channel, but you can use .errors() and .stopOnError() functions to handle them
  * if errors are not handled and hit a thunck-method, then they will be thrown
---
* with last example i want to show you pattern matching and currying
  * practially every function in highland is curry-able
  * curryied stream has a transform function but no source, which is added later
* also you can see, that you can create streams from arrays (it's underscore replacement, right?)
* .where() filters values on a pattern passed to it
* This is Highland.js. There are many more functions and features in the lib, check it out
---
Streams are awesome!
---
Thank you for your attention!
Questions?

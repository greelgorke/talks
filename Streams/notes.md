Title
---

* hi and welcome
* glad to be here
* favorite topic
* node, but may apply in browser
---

greelgorke
---
* my name
* i'm on twitter
* love to work on and talk about
* working at codecentric
* awesome because they help me to be here
---
Agenda
---
* Talk consist of 3 Parts
* general explanation
* show case about versatility
* 3 Tools i know about a little
* What is a Stream? __ask__
* __go through cloud__
---

Stream is
---
* abstraction of sequence of data distributed in time
* potentially infinite
* array is data in space/memory
* pull or push based
* producer forwards pressure to consumer
* producer holds pressure, consumer releases it
* Wasserhahn und Brunnen ?
* pipe = glue for streams
---

* In javascript a stream is a sequence of messages
* a message can hold anything
* this is why streams are versatile

---
Versatile Streams
---
* you can use streams for all of this purposes
* let's see some examples
* you don't have to understand the details here
---
Memory Control
---
* here is how we can consume streams in Node
* pull-based
* read the buffer empty
* or pipe it to a writable
---
JIT-Transoforms
---
* here is a simple Transform stream
* replaces all vocals with ''
* Hamburg -> Hmbrg
* use in a pipe chain
* pipe returns destination stream
---
Module Interface
---
* one-function modules ideally
* export a stream
* or init function which returns a stream
* btw. second way of implementing a core stream
---
Flow Control
---
* here i use a library, because it's better for flow control
* you don't need to understand it entirely
* every EventEmitter is a Stream
* http server is a stream of 'request' events
* let's make it an official stream
---
* fork() spreads backpressure
* route by method, filter
* in map is the business logic
* filter and map produce new streams of results
* for each request send a response
---
* this is simpler
* same filter for get
* send out a file
* a simple file server
* will see more later
* let'S look at the tools
---
Core
---
* idea: think unix pipes inside a process
* sequence of messages? Specialized EventEmitters
* define a events-protokoll (data, end, readable, drain)
* and put it into .pipe()
* 4 abstract blueprints
* handling of backpressure
* 2 modes (from 0.11) paused by default
* object mode makes them true message streams
* readable-stream is core as module. use with browserify & co
---
* Simple Duplex example
* Constructor Call
* write + cb
* read + push
* ._write, ._read called by internals
---
* Simple example for browser
* wrapping a DOM element into a char-stream
* nothing to do in _read, push only stream
* pausing may be implemented by buffering or omitting of events
---
EventStream
---
* created by Dominic Tarr
* created with node 0.8 streams (v1)
* basic idea: custom streams should be easy as function and array
* transforms (or .map()s) are the key
* merge, split, join
---
* remember devocalize in core?
* it's practically a one-liner now
* could be implemented with .replace
---
* generic transform stream is basically consuming and re-emitting
* push based streams with pause capability
* many of simple and complex streams on npm
---
Highland.js
---
* now it's getting functional
* made by Caolan McMahon, async, nodeunit, jam, hoodie
* replacement for async and underscore
* pull-based and lazy evaluated
* thunk-generators (each, toArray, pipe, collect, resume)
* curry the streams
* fork, map seen before
* pattern matching
* higher order streams, stream of streams
* compatible with node core streams: pipe, pause resume
---
* a simple log receiver, http server
* we've seen forking on method
* different fork: split the stream to substreams with pluck
* higher order streams: stream of req
* sequence flattens the next pipe emits data from req to stdout
* second fork just to make http happy
---
* lazy counter
* stream from a function
* push is value emitter, next is an asynchronous terminator
* error propagation through highland in the pipe
* no extra channel, but .errors() and stopOnError()
* not handled errors that hit thunck-methods will throw
---
* last one: pattern matching and curry
* curryied stream has a transform but no source
* streams created from arrays (underscore replacement, right?)
* .where() matches on properties
* many more things in the lib
---
Streams are awesome!
---
Thank you for your attention!
Questions?

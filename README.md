# IDL for ECMAScript

Stage 1

This repository is intended for an investigation into using an Interface Description Language (IDL) in the ECMAScript standard. It is not currently at a stage in TC39.

Thanks to the great efforts in this area from Yehuda Katz, Alex Russell, Brian Terlson, Domenic Denicola, Tobie Langel, Anne van Kesteren, Cameron McCormack and more which made this investigation possible.

## Motivation

### Issues with the current approach

TC39 has been gradually been adding to its standard library using "ecmaspeak" notation, defined in the [Algorithm Conventions](https://tc39.github.io/ecma262/#sec-algorithm-conventions) section of the specification. This provides two main components:
- Methods are declared with a heading, which indicates what object they are a property of, what arguments they take (just as a name) and implies the `"length"` property of the function object.
- Arguments and the receiver are coerced with explicit `ToObject`, `ToString`, `ToLength`, `RequireObjectCoercible`, etc calls; options bags are accessed with `Get`; in general, everything about processing and understanding arguments is interspersed with the definition of algorithms in general.

#### Ease of following conventions in new specifications

The free-form nature of argument handling makes it hard to follow conventions reliably. For example, in Intl, we had several issues where it was unclear what the appropriate way is to process options bags, make constructors and prototype chains work, etc. Ecmaspeak gives many degrees of freedom here, and the conventions aren't formally documented anywhere. Documenting conventions could get us part of the way there, but if we had a language where we could concisely describe the "binding" parts of the interface, it would be easier to stick to those conventions.

#### Auto-generation of bindings in native implementations

JavaScript implementations need to create the object graph for built-ins objects and methods defined by the ECMAScript specification. Currently, this is often done by having either JavaScript or C++ code which explicitly declares all of the objects and methods, and imperatively constructs them.

By contrast, in web browsers, web platform APIs are often described by their WebIDL, and bindings directly to a C++ implementation can be generated. This IDL-based approach has a number of advantages:
- It becomes easier to maintain the code that sets up the object graph and ensure that it matches the specification
- Authors of specifications and implementers can make advancements in a way that's abstracted a bit from the details of JavaScript
- Specification changes can be propagated into the implementation through a copy-paste (modulo browser-specific WebIDL features--work is ongoing to minimize the need for these)

#### "type" definitions for tools

Many tools need to deal with JavaScript's standard library, such as:
- Code completion in IDEs
- Type systems like TypeScript and Flow
- Bindings between JavaScript and other programming languages, like wasm-bindgen

These tools can often use WebIDL as a "type definition" for web specifications. However, for the JavaScript standard library, extra resources are needed. We end up with N x M work, duplicating the definitions per tool per library feature.

## Requirements

The below is just some initial notes on requirements for IDL for JavaScript; these initial thoughts will need to be elaborated in more detail over the course of the Stage 1 investigation. The focus here is on mismatches vs WebIDL.

### Casts of arguments

In ECMAScript methods, arguments and the receiver are usually converted to or checked to be of a particular "type", for example, with operations like ToUint32. These could be conceptually part of the "type declaration" in IDL.

#### Laziness

Sometimes, arguments are coerced "lazily", in the middle of the algorithm, rather than up-front. The idea here is to match how a JavaScript programmer would implement the algorithm. This laziness differs from WebIDL, which does all the coercions and checks when entering the method.

### Overloading

JavaScript constructors are often overloaded on different argument types. The overloading pattern here might or might not match WebIDL's restriction on mixed arguments.

### Matching JS conventions

There are lots of little things that need to be right here, e.g., methods in JavaScript builtins are non-enumerable, whereas they are enumerable in WebIDL.

### Intuitive syntax

WebIDL syntax can look strange to JavaScript developers. We may want to ensure that the IDL used in ECMAScript uses sufficiently intuitive syntax.

## Which language?

### WebIDL

[WebIDL](https://heycam.github.io/webidl/) is a language used by web specifications to declare interfaces and coercions. Some advantages of using WebIDL in ECMAScript are:
- Specifications could be developed in a more uniform way, regardless of venue, enabling more sharing and cross-pollination
- Some of the infrastructure for WebIDL may be reused for JavaScript "for free"
- The additional features of WebIDL to support JavaScript may be useful for other uses of WebIDL; improvements to this standards infrastructure could be broadly beneficial.
- From the perspective of minimizing complexity, it's simpler if the universe has just one IDL in it, rather than two.
- When non-web, non-Ecma systems want an IDL (e.g., Node.js, or systems embedding a web engine with custom C++ bindings), it would be clearer that WebIDL is the universal choice.

The idea to use WebIDL in ECMAScript has been received positively by WebIDL's current maintainers. See [the tracking label](https://github.com/heycam/webidl/labels/jsidl) for issues related to supporting JavaScript.

### JSIDL

[Previous attempts](https://github.com/w3ctag/jsidl) to solve this problem involved proposing a new IDL for ECMAScript. Some reasons for going down this path are:
- The feature set needed by JavaScript is different from the Web. They follow different conventions, e.g., enumerability, overloading, the types of conversions, etc. Overlap is only partial.
- In practice, some of the software handling JSIDL will be different from software which uses WebIDL (e.g., certain JS engines may not be able to reuse the WebIDL generator for web APIs, and need a separate generator)
- From the perspective of minimizing complexity, JSIDL would be a smaller definition than WebIDL as a whole.

### Let's put off the decision

There are TC39 members who are strong supporters of each the WebIDL and JSIDL paths. The intention of this proposal repository is to start by collecting requirements and drafting what we want the ECMAScript specification to look like, and using the results of that investigation, determine whether we want to pursue using WebIDL with extensions, or use a separate JSIDL.

## Plan from here

In terms of the TC39 Stage Process, the following milestones are planned with respect to the stage process, when *entering* each stage:

1. Entering Stage 1: We agree we want to discuss the topic
    * During Stage 1: Discussion of requirements, work on technical alternatives -- current point
2. Entering Stage 2: Agreement on WebIDL vs JSIDL; first draft of IDL itself; IDL conversion drafted for at least one major component of JS
    * During Stage 2: Formalize more of the JS specification as IDL and formalize the IDL definition itself; experiment with automated use of IDL in tools
3. Entering Stage 3: Entire specification is converted to IDL, and IDL definition is fully rigorous; at least one tool is making some use of the IDL
    * During Stage 3: Make further use of IDL in tools, tests and native implementations
4. Entering Stage 4: At least one native implementation uses code generated from the IDL; at least two native implementations implemented any/all normative changes; test262 uses IDL to exhaustively test coercions; PR against the entire specification is prepared

# IDL for ECMAScript

This repository is intended for an investigation into using an Interface Description Language (IDL) in the ECMAScript standard.

## Motivation

### Why expand the standard library of JavaScript in TC39?

### Issues with the current approach

#### Ease of following conventions in new specifications

#### Auto-generation of bindings in native implementations

#### 

## Requirements

### Casts of arguments

#### Laziness

### Overloading

### Matching JS conventions

## Which language?

### WebIDL

### JSIDL

## Plan from here

In terms of the TC39 Stage Process, the following milestones are planned with respect to the stage process, when *entering* each stage:

1. Entering Stage 1: We agree we want to discuss the topic
    * During Stage 1: Discussion of requirements, work on technical alternatives
2. Entering Stage 2: Agreement on WebIDL vs JSIDL; first draft of IDL itself; IDL conversion drafted for at least one major component of JS
    * During Stage 2: Formalize more of the JS specification as IDL and formalize the IDL definition itself; experiment with automated use of IDL in tools
3. Entering Stage 3: Entire specification is converted to IDL, and IDL definition is fully rigorous; at least one tool is making some use of the IDL
    * During Stage 3: Make further use of IDL in tools, tests and native implementations
4. Entering Stage 4: At least one native implementation uses code generated from the IDL; at least two native implementations implemented any/all normative changes; test262 uses IDL to exhaustively test coercions; PR against the entire specification is prepared

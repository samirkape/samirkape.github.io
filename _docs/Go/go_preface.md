---
title:  "Introduction"
---

#### Go Project

> Go was intended as a language for writing server programs that would be easy to maintain over time
> 
> Rob Pike says, Complexity is multiplicative
> 
> Only through simplicity of design a system can remain stable, coherent and secure as it grows.

1.  The Go project was borne of frustration with several software systems at Google that were suffering from an explosion of complexity
    
2.  As a recent high-level language, Golang has the benefit of hindsight. It has
    
    1.  Garbage Collector
        
    2.  Package System
        
    3.  [First Class Functions](https://en.wikipedia.org/wiki/First-class_function)
        
    4.  [Lexical Scope](https://whatis.techtarget.com/definition/lexical-scoping-static-scoping#:~:text=Lexical%20scoping%20)
        
    5.  System Call Interface
        
    6.  UTF-8 Encoded Immutable Strings, So they can contain characters from any language
        
3.  On the other hand, Creators has decided not to add,
    
    1.  Implicit Numeric Conversion
        
    2.  Constructor / Destructor
        
    3.  Operator Overloading
        
    4.  Default Parameters
        
    5.  Inheritance
        
    6.  [Generics](https://golang.org/doc/faq#generics)
        
    7.  [Exceptions](https://golang.org/doc/faq#exceptions)
        
    8.  Macros
        
    9.  [Function Annotations](https://www.python.org/dev/peps/pep-3107/)
        
4.  **Built-in data types** and most library data structures are crafted to work naturally **without explicit initialization** or implicit constructors
    
5.  Go gives programmers much of the safety and run-time performance benefits of a relatively strong type system without the burden of a complex one (Like C++)
    
6.  Go has concurrency features based on [CSP](https://cs.stanford.edu/people/eroberts/courses/soco/projects/2008-09/tony-hoare/csp.html). The variable size stacks of Go’s lightweight threads or goroutines are initially small enough that creating one goroutine is cheap and creating a million is practical
    
7.  Go’s [standard library](https://pkg.go.dev/std) has APIs for I/O, text processing, graphics, cryptography, networking, and distributed applications, with support for many standard file formats and protocols
    

#### Book Organization

##### Chapter 1

It will have a tutorial on **the basic constructs** of Go. Like,

1.  Reading / Writing Files
    
2.  Formatting text
    
3.  Creating Images
    
4.  Communicating with Internet Client and Servers
    

##### Chapter 2

This chapter describes the **structural elements** of a Go program

1.  Declarations
    
2.  Variables
    
3.  Packages
    
4.  New types
    
5.  Files
    
6.  Scope
    

##### Chapter 3

This will give knowledge about **Basic Data** types

1.  Numbers
    
2.  Booleans
    
3.  Strings
    
4.  Constants
    
5.  How to process Unicode
    

##### Chapter 4

It describes **Composite Data Types**

1.  Arrays
    
2.  Maps
    
3.  Structs
    
4.  Slices
    
5.  Go’s approach to dynamic lists
    
6.  JSON
    
7.  Text and HTML Templates
    

##### Chapter 5

It will uncover the **Functions, Error Handling and Panic, Recover and Defer**

1.  Multiple return value
    
2.  Recursion
    
3.  Errors
    
4.  Function Values
    
5.  Anonymous Functions
    
6.  Variadic Functions
    
7.  Deferred Function Calls
    
8.  Panic
    
9.  Recover
    
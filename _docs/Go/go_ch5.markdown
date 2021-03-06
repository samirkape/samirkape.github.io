---
 title:  "Chapter 5"
 description: "From The Go Programming Language"
---

- [Random Notes](#random-notes)
- [5 Functions](#5-functions)
  - [5.1 Declaration](#51-declaration)
  - [5.3 Multiple Returns](#53-multiple-returns)
  - [5.4 Error Handling](#54-error-handling)
  - [5.4.1 EOF](#541-eof)
  - [5.5 Function Values](#55-function-values)
  - [5.6 Anonymous Functions](#56-anonymous-functions)
  - [5.7 Variadic Functions](#57-variadic-functions)
  - [5.8 Deferred Function Calls](#58-deferred-function-calls)

### Random Notes


*   The golang.org/x/... repositories hold packages designed and maintained by the Go team for applications such as networking, internationalized text processing, mobile platforms, image manipulation, cryptography, and developer tools. These packages are not in the standard library because they’re still under development or because they’re rarely needed by the majority of Go programmers.
    
*   Convention: In case of multiple returns, if you want to return an error, it should be a last parameter to return
    
*   If the failure has only one possible cause, the result is boolean, usually called ok
    
*   We can use fmt.Errorf( ) to format the error msg in which, err string can be represented using %v.
    

Useful when you want to append your message with err

```
fmt.Errorf("parsing %s as HTML: %v", url, err)
```

*   Difference between fmt and log package for printing an error
    

With all the log functions, by default, date and time are prefixed to the message string

```
if err := WaitForServer(url); err != nil {
         log.Fatalf("Site is down: %v\n", err)
}
//2006/01/02 15:04:05 Site is down: no such domain: bad.gopl.io
```

*   All log functions append a new line if one is not already present.
    
*   If you want to **print type %T of anything**, fmt.Println won’t work (Prints address); you have to use fmt.Printf
    
*   **The suffix f** is a widely followed naming convention for variadic functions that accept a Printf-style format string .
    
* * *

### 5 Functions


The running example of this chapter is a web crawler, that is, the component of a web search engine responsible for fetching web pages, discovering the links within them, fetching the pages identified by those links, and so on. A web crawler gives us ample opportunity to explore recursion, anonymous functions, error handling, and aspects of functions that are unique to Go.

**Stack**

Typical Go implementations use variable-size stacks that start small and grow as needed up to a limit on the order of a gigabyte. This lets us use recursion safely and without worrying about overflow.

* * *

#### 5.1 Declaration


Like parameters, results may be named. In that case, each name declares a local variable

initialized to the zero value for its type.

```
func sub(x, y int) (z int)   { z = x - y; return }
```

*   Are there default parameters or named parameters?
    

No. Every function call must provide an argument for each parameter with its original sequence, Go has no concept of default parameters or named parameters

*   What are parameters?
    

Parameters are the local variables within the body of a function with their initial value set to the arguments supplied by the caller.

*   What are arguments and pass by value?
    

Arguments are passed by value, so the function receives a copy of each argument; modifications to copy do not affect the caller.

*   Exceptions to pass by value rule?
    

Reference data structures like,

1.  Pointer
    
2.  Slice
    
3.  Map
    
4.  Function
    
5.  Channel
    
* * *

#### 5.3 Multiple Returns


```
 // CountWordsAndImages does an HTTP GET request for the HTML
 // document url and returns the number of words and images in it.

func CountWordsAndImages(url string) (words, images int, err error) {
         resp, err := http.Get(url)
         if err != nil {
          return 
         }
         doc, err := html.Parse(resp.Body)
         resp.Body.Close()
         if err != nil {
             err = fmt.Errorf("parsing HTML: %s", err)
             return 
          }
         words, images = countWordsAndImages(doc)
         return. // we can omit names 
}
```
* * *

#### 5.4 Error Handling


When a function call returns an error, it is a caller’s responsibility to check it and handler.

Let’s take a look at five of such situations

1.  _**Propagate** -_ Failure in a subroutine becomes a failure of calling routine
    

When a propagated error is handled by the main function, it should provide a clear causal chain from the root problem to overall failure.

```
genesis: crashed: no parachute: G-switch failed: bad relay orientation
```

Notice that there’s no newline and stack trace involved so conventionally we should follow the below rules for reporting an error

*   Message strings should not be capitalized
    
*   No newlines
    

2. _**Retry** \-_ For errors that represent transient or unpredictable problems, it may make sense to **retry the failed operation**, Example use case may be sending an HTTP request to a server that is currently inaccessible (e.g unipune).

3\. _**Exit**_ - If progress is impossible, the caller can print the message and **exit** gracefully. But this course of action should generally be **reserved for the main package** of a program. **Library functions should propagate errors to the caller.**

```
// (In function main.)
if err := WaitForServer(url); err != nil {
        fmt.Fprintf(os.Stderr, "Site is down: %v\n", err)
        os.Exit(1) 
}
```

Difference between fmt and log package for printing an error

With all the **log functions**, by default, **date and time are prefixed** to the message string

```
if err := WaitForServer(url); err != nil {
         log.Fatalf("Site is down: %v\n", err)
}
//2006/01/02 15:04:05 Site is down: no such domain: bad.gopl.io
```

For a more attractive output, we can set the prefix used by the log package to the name of the

command, and **suppress the display of the date and time**:

```
log.SetPrefix("wait: ")
log.SetFlags(0)
```

4\. _**Just Print** \-_ in some cases, it’s sufficient just to log the error and then continue, perhaps with reduced functionality. Again there’s a choice between using the log package, which adds the usual prefix:

```
if err := Ping(); err != nil {
         log.Printf("ping failed: %v; networking disabled", err)
}
```

and printing directly to the standard error stream:

```
if err := Ping(); err != nil {
         fmt.Fprintf(os.Stderr, "ping failed: %v; networking disabled\n", err)
}
```

5\. _**Ignore** \-_ In some cases we can safely ignore errors but with documenting the intention

```
dir, err := ioutil.TempDir("", "scratch")
if err != nil {
    return fmt.Errorf("failed to create temp dir: %v", err)
}
// ...use temp dir...
os.RemoveAll(dir) // ignore errors; $TMPDIR is cleaned periodically
```

* * *

Error Handling Tips

*   After checking an error, failure is usually dealt with before success
    
*   If failure causes the function to return, the logic for success is not indented within an else block but follows at the outer level
    
*   Functions tend to exhibit a common structure, with a series of initial checks to reject errors, followed by the substance of the function at the end, minimally indented.
    
* * *

#### 5.4.1 EOF


```
in := bufio.NewReader(os.Stdin)
for {
    r, _, err := in.ReadRune()
    if err == io.EOF {
        break // finished reading
    }
    if err != nil {
        return fmt.Errorf("read failed: %v", err)
    }        // ...use r...
```
* * *
#### 5.5 Function Values


What do you mean by functions are the _first-class_ values?

Like other values function values have types,

they may be,

*   Assigned to variables
    
*   Passed to functions
    
*   Return from functions
    
*   Function values can be called like any other function
    

```
func add(x, y int) int     { return x + y }
func square(n int) int     { return n * n }
f := add
fmt.Println(f(2, 4)) // 6
f = square // compile error: can't assign f(int, int) int to f(int) int
```

*   Can be compared with nil
    
* * *
#### 5.6 Anonymous Functions


Named functions can be declared only at the package level, but we can use a function literal to denote a function value within any expression.

What is a function literal?

A function literal is written like a function declaration but without a name following func keyword

```
strings.Map(func(r rune) rune { return r + 1 }, "HAL-9000")
```
* * *
#### 5.7 Variadic Functions


*   What are the variadic functions?
    

A variadic function is the one that can be called with a varying number of arguments.

*   Example?
    

Printf – Requires one fixed argument

```
func f(...int) {}
func g([]int)  {}
fmt.Printf("%T\n", f) // "func(...int)"
fmt.Printf("%T\n", g) // "func([]int)" 
```

Although the ...int parameter behaves like a slice within the function body, the type of a variadic function is distinct from the type of a function with an ordinary slice parameter.

*   How does it work?
    

Implicitly, the caller allocates an array, copies the arguments into it, and passes a slice of the entire array to the function.

*   Most common use cases?
    

Variadic functions are often used for string formatting

The suffix f is a widely followed naming convention for variadic functions that accept a Printf-style format string .

* * *

```
package main

import "fmt"

func f(...int) {}
func g([]int)  {}

func main() {
	s := []int{1, 2, 3, 4, 5}
	fmt.Printf("%d\n", sum(s...))
	fmt.Printf("%d\n", slice_sum(s))
}

func sum(vals ...int) int {
	total := 0
	for _, val := range vals {
		total += val
	}
	return total
}

func slice_sum(vals []int) int {
	total := 0
	for _, val := range vals {
		total += val
	}
	return total
}
```

* * * 

#### 5.8 Deferred Function Calls


**use case**

When you have a resource (such as http.Get(), and opened file ) that needs to be closed explicitly, you may close it in multiple locations in the same function. This makes it hard to maintain the program

**defer(Keyword) statement**

*   The function and argument expressions are evaluated when the statement is executed, but the actual call is deferred until the function call that contains the deferred statement has finished,
    
    *   Normally
        
    *   Executing return statement
        
    *   Falling off the end
        
    *   Abnormally, by panicking
        
*   Any number of function calls can be deferred
    
    *   They are **executed in the reverse** order in which they are deferred
        
    *   A defer statement is **often used with pair operations**, to ensure that the resources are released in all cases, no matter how complex the control flow. e.g
        
        *   Open / Close
            
        *   Connect / Disconnect
            
        *   Lock / Unlock
            

**Where to use?**

The right place for a defer statement that releases a resource is immediately after the resource has been successfully acquired.

**Example**

_Before using defer_

```
func title(url string) error {
         resp, err := http.Get(url)
         if err != nil {
            return err 
         }
         // Check Content-Type is HTML (e.g., "text/html; charset=utf-8").
         ct := resp.Header.Get("Content-Type")
         if ct != "text/html" && !strings.HasPrefix(ct, "text/html;") {
             resp.Body.Close()
             return fmt.Errorf("%s has type %s, not text/html", url, ct)
         }
         doc, err := html.Parse(resp.Body)
         resp.Body.Close()
         if err != nil {
             return fmt.Errorf("parsing %s as HTML: %v", url, err)
         }
         visitNode := func(n *html.Node) {
             if n.Type == html.ElementNode && n.Data == "title" &&
                 n.FirstChild != nil {
                 fmt.Println(n.FirstChild.Data)
             }
         }
         forEachNode(doc, visitNode, nil)
         return nil
}
```

_after using defer_

```
func title(url string) error {
         resp, err := http.Get(url)
         if err != nil {
            return err 
         }
         defer resp.Body.Close()
         ct := resp.Header.Get("Content-Type")
         if ct != "text/html" && !strings.HasPrefix(ct, "text/html;") {
             return fmt.Errorf("%s has type %s, not text/html", url, ct)
         }
         doc, err := html.Parse(resp.Body)
         if err != nil {
             return fmt.Errorf("parsing %s as HTML: %v", url, err)
         }
         // ...print doc's title element...
        return nil 
}
```
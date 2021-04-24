---
 title:  "Chapter 1 & 2"
 description: "From The Go Programming Language"
---

- [Program Structure](#program-structure)
  - [2.1 Names](#21-names)
  - [2.2 Declarations](#22-declarations)
  - [2.3 Variables](#23-variables)
    - [2.3.1 Short Variable Declarations](#231-short-variable-declarations)
    - [2.3.2 Pointers](#232-pointers)
    - [2.3.3 The new Function](#233-the-new-function)
    - [2.3.4. Lifetime of Variables](#234-lifetime-of-variables)
  - [2.4 Assignment](#24-assignment)
  - [2.5 Type Declarations](#25-type-declarations)
  - [2.6 Packages and Files](#26-packages-and-files)
  - [2.6.1 Imports](#261-imports)
  - [2.6.2 Package Initialization](#262-package-initialization)
- [Tutorial](#tutorial)
  - [1.1 Hello World](#11-hello-world)
  - [1.2 Command Line Arguments](#12-command-line-arguments)
  - [1.3 Finding Duplicate Lines ( Using stdin for input text )](#13-finding-duplicate-lines--using-stdin-for-input-text-)
    - [1.3.1 Finding Duplicate Lines ( Using files/stdin for input text )](#131-finding-duplicate-lines--using-filesstdin-for-input-text-)
    - [1.3.2 Finding Duplicate Lines ( Reading files with ioutil )](#132-finding-duplicate-lines--reading-files-with-ioutil-)
  - [1.4 Animated Gifs](#14-animated-gifs)
  - [1.5. Fetching a URL](#15-fetching-a-url)
  - [1.6. Fetching URLs Concurrently](#16-fetching-urls-concurrently)
  - [Uncategorized](#uncategorized)

### Program Structure

#### 2.1 Names


Go has

_25 keywords_

|     |     |     |     |     |
| --- | --- | --- | --- | --- |
| break | default | **func** | **interface** | **select** |
| case | **defer** | **go** | **map** | struct |
| **chan** | else | goto | **package** | switch |
| const | **fallthrough** | if  | **range** | **type** |
| continue | for | **import** | return | **var** |

_3 Dozen predeclared names_

|     |     |
| --- | --- |
| Constants | true, false, iota, nil |
| Types | int, int8, int16, int32, int64<br><br>uint, uint8, uint16, uint32. uint64, **uintptr**<br><br>float32, float64, **complex64, complex128**<br><br>bool, byte, **rune**, string, error |
| Functions | **make, len, cap, new, append, copy**, close<br><br>complex, real, imag<br><br>**panic, recover** |
| These names are not reserved, so you may use them in declarations, but beware of the confusion |     |

*   If an entity is declared outside of a function it is **visible** to **all the files of the package to which it** belongs
    
*   The case of the first letter of the word determines its visibility across package boundaries. If the name begins with an upper case letter, it is _exported,_ which means that it is visible and accessible outside of its own package
    
*   Package name themselves always in lower-case letter
    

* * *

#### 2.2 Declarations



*   There are 4 major types of declarations
    
    *   var
        
    *   const
        
    *   type
        
    *   func
        
*   In this section, we will see var and type
    

```
package main

import "fmt"

const boilingF = 212.0

func main() {

var f = boilingF

var c = (f - 32) * 5 / 9

fmt.Printf("boiling point = %g°F or %g°C\n", f, c)
// Output:
// boiling point = 212°F or 100°C
}
```

*   _const boilingF = 212.0_ is a package level declaration
    
*   The name of the package level declaration is visible not only throughout the source file that contains its declaration but throughout all the files of the package
    

#### 2.3 Variables



*   keyword: _var_
    
*   The role of a _var_ declaration,
    
    *   Creates a variable of a particular type
        
    *   Attaches name to it
        
    *   Sets initial value
        
*   Syntax
    

```
var name type = expression
```

*   The initial value is the zero value for the type,
    
    *   0 for numbers,
        
    *   false for booleans,
        
    *   "" for strings,
        
    *   nil for interfaces and reference types (slice, pointer, map, channel, function).
        
    *   The zero value of an aggregate type like an array or a struct has the zero value of all of its elements or fields
        
*   In Go, there is no such thing as an uninitialized variable
    
*   It is possible to declare and optionally initialize a set of variables in a single declaration, with a matching list of expressions. Omitting the type allows declaration of multiple variables of different types:
    

```
var i, j, k int // int, int, int
var b, f, s = true, 2.3, "four" // bool, float64, string
```

##### 2.3.1 Short Variable Declarations



Within a function, an alternate form called a short variable declaration may be used to declare and initialize **local variables**

```
name := expression
```

* * *

**A var declaration**

Tends to be reserved for local variables that _need an explicit type that differs from that of the initializer expression_

```
e.g
i := 100 // by default an int
var f float64 = 100 // a float64 
j := 100.0 // TBD
```

or when the variable will be assigned a value later and its initial value is unimportant

* * *

**Multiple variables can be declared and initialized**

```
i, j := 0, 1
```

But use it only when it helps readability such as, for loop initializations

Keep in mind that,

```
:= // is a declaration
=  // is a assingment

i, j := 0, 1 // is a declaration
i, j = j, i  // swap values of i and j
```

* * *

**A subtle but important point,**

A short variable does not necessarily declare all the variables on its left-hand side. If **some** of them were already declared in the same lexical block, then the short variable declaration acts as an assignment and assigns a new value to those variables

```
  in, err := os.Open(file1) // in and err declared
  ...
  out, err := os.Create(file2) // err will be assigned with new value 
  ...
  // But it requires at least a one new variable
  out, err := os.Create(file3)  // Compile error: no new variables 
```

##### 2.3.2 Pointers



A variable is a piece of storage containing a value

A Pointer value is an address of a variable

The pointer thus the location where the address of a variable is stored

Not every value has an address but every variable does

With a pointer, we can read or update the value of a variable _indirectly_

The zero value for the pointer type is nil

Pointers are comparable for the following conditions

*   They are pointing to the same location
    
*   They both are nil
    

**It is perfectly safe for the function to return the address of a local variable**

```
var ptr = f()

func f() *int {
  x := 5
  return &x
} 

each call of f() returns distinct value hence 
f() == f() is false
this is because at each call variable x gets new allocation
```

* * *

Because pointer contains an address of a variable, It can be passed to the function to modify the value of the variable indirectly

Each time we take the address of a variable or copy a pointer, we create new aliases or ways to identify the same variable. For example, \*ptr is an alias for x

Pointer aliasing is useful because it allows us to access the variable without using its name, but its a double-edged sword: to find all the statements which modified the value of a variable we should know all its aliases.

Reference types: Slices, arrays, interfaces, structs, maps, channels

* * *

Pointers are the key to the flag package, which uses the program’s command-line arguments to set the values of certain variables distributed throughout the program

bool = flag.Bool( ) creates the flag variable with type bool which takes below arguments,

*   Name of the flag
    
*   The variable's default value
    
*   A message that will be printed if a user provides an invalid flag or -h or -help
    

string = flag.String( )

*   Name of the flag
    
*   The variable's default value
    
*   A message that will be printed if a user provides an invalid flag or -h or -help
    

```
package main

import(
	"fmt"
	"flag"
	"strings"
)

var n = flag.Bool( "n", false, "Omit trailing newline" )
var sep = flag.String( "s", " ", "Separator" )

func main() {
	
	flag.Parse()
	fmt.Print(strings.Join(flag.Args(), *sep))

	if !*n {
		fmt.Println()
	}
}
```

##### 2.3.3 The new Function



new – A built-in function. Another way to create a variable

new(T)

*   is the expression, which creates an unnamed variable of type T
    
*   Initializes it to zero value of the type
    
*   Returns its **address of type \*T**
    

```
p := new(int) // p, of type *int, points to an unnamed int variable
fmt.Println(*p) // "0"
*p = 2 // sets the unnamed int to 2
fmt.Println(*p) // "2"
```

Each call to a new function returns a variable with a distinct address

Two exceptions to this rule,

*   Two variables whose implementation carries no information and is of size zero, such as
    
*   struct{ }
    
*   \[0\]int
    
*   May have the same address
    

Since new is a predeclared function and not a keyword, you can use its name to declare a variable or function.

```
func delta(old, new int) int {
return new - old 
} 
```

**But keep in mind that, you won’t be able to use new( ) in this function**

##### 2.3.4. Lifetime of Variables



**A compiler may choose to allocate local variables on heap or stack**, Surprisingly this choice is not determined by whether new or var was used to declare the variable

```
var global *int

func f() {
  var x int
  x = 1
  global = &x
}
```

Here **x must be allocated on the heap** because it is still reachable from variable global after f( ) has returned

```
func g() {
  y := new(int)
  *y = 1
}
```

Here **y will be allocated on the stack** because It is not referenced outside of the function

* * *

In any case, the notion of escaping is not something that you need to worry about in order to write correct code, though it’s good to keep in mind during performance optimization since each variable that escapes requires an extra memory allocation

Garbage collection is a tremendous help in writing correct programs, but it does not relieve you of the burden of thinking about memory

You don’t need to explicitly allocate and free memory, but to write efficient programs you still need to be aware of the lifetime of variables.

For example, keeping unnecessary pointers to short-lived objects within long-lived objects, especially global variables, will prevent the garbage collector from reclaiming the short-lived objects.

#### 2.4 Assignment



The value held by a variable is updated by an assignment statement

In its simplest form has a variable on the left side of the = operator and an expression on the right.

Numeric Variables can be incremented and decremented by ++ and – – operator, like a++ and a– –

**Tuple Assignment –**

Another form of assignment known as tuple assignment that allows –  
several variables to be assigned at once

How?

– all of the right-hand side expressions are evaluated before any of the variables are updated

```
//GCD using tuple assignment
package main

func main() {
  gcd := find_gcd(5, 10)
}

func find_gcd( num1, num2 int ) int {
  for num2 != 0 {
    num1, num2 = num2, num1 % num2
  }
  return num1
}
```

**Assignability**

Whether two values may be compared with == and != is related to assignability: in any comparison, the first operand must be assignable to the type of the second operand, or vice versa

#### 2.5 Type Declarations



type declaration –

What does it do? – Defines a **new named type**

Property of that named type? – Has the same underlying type as an existing type

What does it offer? – Provides a way to separated different perhaps incompatible uses of (same) underlying type so that they cant be mixed unintentionally

```
type name underlying-type
type Celcius float64
```

Type declarations most often declared at the package level, where the named type is visible throughout the package, and if the name is exported ( if the first letter is capital ) it is accessible from other packages as well

```
// Using type declarations to convert degree C to F

package tempconv

type Celsius float64
type Fahrenheit float64

const (
	AbsoluteZero = -273.15
	Freezing     = 0
	Boiling      = 100
)

func CtoF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }
func FtoC(f Fahrenheit) Celsius { return Celsius((f - 32) * 9 / 5) }
```

Fahrenheit( ) / Celsius( ) – type conversions, not a function call

In any case, a conversion never fails at runtime

```
var c Celsius
var f Fahrenheit
fmt.Println(c == 0) // "true"
fmt.Println(f >= 0) // "true"
fmt.Println(c == f) // compile error: type mismatch
fmt.Println(c == Celsius(f)) // "true"!
```

#### 2.6 Packages and Files



– Packages in Go serves the same purpose as libraries and modules in other languages supporting,

1.  Modularity
    
2.  Encapsulation
    
3.  Reusability
    
4.  Separate Compilation
    

– The source code of a package resides in one or more .go files

– Usually in the directory whose name ends with the import path

For instance,

– The files of **gopl/ch1/helloworld** package are stored in,

**$GOPATH/src/**gopl/ch1/helloworld

– Packages also let us hide the information by,

– Controlling which names are visible outside the package or exported and which are not

– Exported elements start with an upper-case letter

* * *

How to create package?

1.  In the file of which you want to export symbols, enter _package name_ as a name of the package
    
2.  Every function or variable you want to export should start with an upper-case letter
    
3.  In case you don’t want to export the symbol, make its first letter lower-case
    
4.  Now, move that package into $GOPATH/src/_parent-dir/package-name_
    
5.  By convention, a package’s name matches the last segment of its import path, making it easy to predict that the package name of gopl/ch2/tempconv is tempconv
    
6.  Hence if your package file name is tempconv and in that file, the package is defined by _package temp._ It has to be referred by _package tempconv_
    

#### 2.6.1 Imports



When using the go tool (Chapter 10), an import path denotes a directory containing one or more Go source files that together make up the package

By convention, a package’s name matches the last segment of its import path, making it easy to predict that the package name of gopl/ch2/tempconv is tempconv

#### 2.6.2 Package Initialization



If the package has multiple .go files, they are initialized in the order in which the files are given to the compiler; the go tool sorts .go files by name before invoking the compiler

For some variables, like tables of data, an initializer expression may not be the simplest way to set its initial value; In that case, the init function mechanism might be the simpler.

Any file may contain any number of functions whose declaration is just

```
func init() { /* ... */ }
```

Such init functions can’t be called or referenced, but otherwise, they are normal functions.

Within each file, **init functions are automatically executed when the program starts**, in the order in which they are declared

One package is initialized at a time,

* * *

### Tutorial

#### 1.1 Hello World



1.  Go is a compiled language and it has a toolchain to convert a source code to an executable binary. These tools are accessed by a single command called **go** that has a number of subcommands
    
2.  the **go run** command performs the following three activities
    
    1.  Compiles one or more specified source files with ext .go
        
    2.  Links it with the libraries
        
    3.  Run the executable
        
3.  e.g **go run hello\_world.c**
    
4.  hello world observations
    
    1.  have to specify the package name in our case it was main
        
    2.  to import some packages from the standard library we have to specify it in import()
        
    3.  Every function starts with keyword **func**
        
    4.  we have to provide opening curly on the same line as the **func**
        
5.  If you want to build your project, fire **go build** **hello\_world.c**
    
6.  Go code is organized into packages, which are similar to libraries or modules in other languages
    
7.  A package consists of one or more .go file in a single directory which specifies what the package does
    
8.  Each source file begins with the keyword **package**
    
9.  In hello\_world.go case the 'package main' specifies which package the file belongs to, followed by a list of packages to be imported
    
10.  **package main** defines standalone executable program not a library
    
11.  We must tell the compiler what packages are required by this source file by specifying them in an **import**
    

```
import(
  "fmt"
  "os"
)
```

Notice there’s no comma or semicolon after specifying each package

1.  We must import exactly the packages we need. **Importing less or more than required will cause compilation to fail**
    
2.  **goimports** additionally manages the insertion and removal of import declarations as needed
    
3.  Go **does not require** you to specify a **semicolon** at the end of the declaration or statement, except **where two or more appear on the same line**
    
4.  **gofmt** will format the go source file
    

#### 1.2 Command Line Arguments



1.  The **os** package provides functions and values to deal with the OS in a platform-independent way
    
2.  The command-line arguments are provided to program in the _variable_ **Args** which is a part of the os package and can be accessed by the statement **os.Args**
    
3.  The variable **os.Args** is a _slices_ of the strings
    
4.  What is slice: Think of it as a dynamically sized sequence _s_ of array elements where,
    
    1.  Individual elements can be accessed by s\[i\] notion
        
    2.  and contiguous subsequence as s\[m:n\]
        
    3.  As in most other programming languages, All indexing in Go uses _half-open_ intervals that includes the first index ( m ) and excludes the last ( n )
        
    4.  for example, s\[m:n\] contains n - m elements
        

```
package main
//this code prints the program's command line arguments 

import(
        "os"
        "fmt"
)

func main(){
        var s, sep string
        for i := 1; i < len(os.Args); i++ {
                s += sep + os.Args[i]
                sep = " "
        }
        fmt.Println(s)
}
```

5\. Notice the uninitialized variables _s, sep._ Go implicitly initializes all the variables by default to zero value of its type ( 0 for numeric types and ““ empty string for strings ), if they are uninitialized

6\. **Bonus:** Unlike other languages where i++ is an expression ( means you can assign its output to another variable like j = i++ ), In Go, it is a statement. Also, it is only a postfix statement. ++i is illegal

7\. _for loop_ is the only loop statement in Go

```
// Syntax 1 - typical for loop

for initialization; condition; post{  
  // Zero or more statements
}

// 1. Braces are necessary
// 2. Opening brace must be on the same line as the post statement
// 3. initialization statement is optional - executed before the loop starts
// 4. condition is a boolean expression - evaluated at the beginning of each iteration
// 5. post statement is - executed after the body of loop

// Syntax 2 - for loop with range

for _, arg := range os.Args[1:] {
  // body 
} 

// 1. range produces two values, 1st - index, 2nd - element
// 2. We don't want to use index but we have to receive it in some tmp variable
// 3. As Go doesn't allow unused local variables we have to use blank identifier '_'
// 4. os.Args[1:] skips first element                
```

8\. **Bonus:** _Infinite loop_

```
for{
}
```

9\. **Bonus:** _Optimized Version of above CLA program_

```
package main
// prints CLA without for loop using strings.Join()

import(
""
)
```

#### 1.3 Finding Duplicate Lines ( Using stdin for input text )



Using Stdin

```
package main
// this code will find adjacent duplicate lines from stdin
// similar to the uniq command on linux/unix
// will be using bufio and map and make

import(
	"fmt"
	"bufio"
	"os"
)

func main(){
	counts := make(map[string]int)
	input := bufio.NewScanner(os.Stdin)

	for input.Scan() {
		counts[input.Text()]++
	}

	for line, n := range counts {
		if n > 1 {
			fmt.Printf("\n%d\t%s\n", n, line)
		}
	}
}
```

Notes

*   bufio - NewScanner(), input.Scan(), input.Text() - NewScanner() reads input and breaks it into lines or words
    
*   bool = input.Scan() reads newline on each call and removes '\\n'
    
*   type = input.Text() stores the result of input.Scan()
    
*   fmt.Printf() - Produces formatted string based on the input verbs
    
*   fmt.Println() uses %v by default. cannot use verbs
    
*   make()
    
*   make(map) A map is a reference to the data structure created by make()
    
*   map\[string\]int - map holds key value pair and provides constant time operations (store, retrieve, test)
    
*   the key may be of any type whose values can be compared with ==
    
*   A value can be of any type at all
    
*   In practice the order of map iteration is random and can vary run to run
    

##### 1.3.1 Finding Duplicate Lines ( Using files/stdin for input text )



```
package main

//this program will be using Files (on CLA) to read text and report duplicates
// if no CLA is provided the program will go for reading text from stdin

import (
	"bufio"
	"fmt"
	"os"
)

func main() {

	store := make(map[string]int)

	if len(os.Args) > 1 {
		cla_list := os.Args[1:]
		for _, element := range cla_list {
			fd, err := os.Open(element)
			check_err(err)
			scan_text(fd, store)
		}
	} else {
		scan_text(os.Stdin, store)
	}

	for key, element := range store {
		fmt.Printf("key -> %s \t count -> %d\n", key, element)
	}
}

func check_err(err error) {
	if err != nil {
		fmt.Fprintf(os.Stderr, "dup2: %v", err)
	}
}

func scan_text(fd *os.File, counts map[string]int) {

	input := bufio.NewScanner(fd)
	for input.Scan() {
		counts[input.Text()]++
	}

}
```

Notes

*   \*os.File, err = os.Open("filename") - Open file filename for READING
    
*   error type - err can be passed to function as well as printed
    

##### 1.3.2 Finding Duplicate Lines ( Reading files with ioutil )



What does this section offer?

1.  ReadFile from io/ioutil package
    
2.  string.Split()
    

```
//this program will be using Files (on CLA) to read text 
(io/ioutil) and report duplicates


package main

import (
	"fmt"
	"io/ioutil"
	"os"
	"strings"
)

func main() {

	store := make(map[string]int)
	args := os.Args[1:]

	for _, element_f := range args {
		data, err := ioutil.ReadFile(element_f)
		check_err(err)

		for _, word := range strings.Split(string(data), " ") {
			store[word]++
		}
	}
	for word, count := range store {
		if count > 1 {
			fmt.Printf("%s  ->  %d\n", word, count)
		}
	}

}

func check_err(err error) {
	if err != nil {
		fmt.Errorf("1-3-2: ", err)
	}
}
```

ReadFile( )

*   Function - Reads the entire contents of a file
    
*   Syntax - data, err := ioutil.ReadFile(filename)
    
*   Package - io/ioutil
    
*   Input - Filename in string
    
*   Output -
    
    *   byte slice that must be converted into a string
        
    *   err
        

string.Split( )

*   Function - Splits a string into a slice of substrings
    
*   Syntax - string := string.Split(string, “token”)
    
*   Package - strings
    
*   Input - Filename in string
    
*   Output -
    
    *   string
        
    *   err
        

#### 1.4 Animated Gifs



What does this section offer?

1.  Structs
    
2.  Const
    
3.  Composite Literals
    

```
// This program aims to draw Lissajous figures

package main

import (
	"image"
	"image/color"
	"image/gif"
	"io"
	"math"
	"math/rand"
	"os"
)

var palette = []color.Color{color.White, color.Black}

const (
	whiteIndex = 0
	blackIndex = 1
)

func main() {
	lissajous(os.Stdout)
}

func lissajous(out io.Writer) {
	const (
		cycles  = 5
		res     = 0.001
		size    = 100
		nframes = 64
		delay   = 8
	)

	freq := rand.Float64() * 3.0 // relative freq of y oscillator
	anim := gif.GIF{LoopCount: nframes}
	phase := 0.0 //phase difference

	for i := 0; i < nframes; i++ {
		rect := image.Rect(0, 0, 2*size+1, 2*size+1)
		img := image.NewPaletted(rect, palette)

		for t := 0.0; t < cycles*2*math.Pi; t += res {
			x := math.Sin(t)
			y := math.Sin(t*freq + phase)
			img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5), blackIndex)
		}

		phase += 0.1
		anim.Delay = append(anim.Delay, delay)
		anim.Image = append(anim.Image, img)
	}
	gif.EncodeAll(out, &anim)
}
```

Notes -

*   The value of a constant must be a number, string , or boolean
    
*   \[ \]color.Color{ } and gif.GIF{ } are composite literals
    
    *   \[ \]color.Color{ } is a _slice_
        
    *   gif.GIF{ } is a _struct_
        
*   The individual fields of a struct can be accessed using dot notation
    

#### 1.5. Fetching a URL



```
// this utility will fetch the URL specified on CL
// refer >> man curl for more details

package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "os"
)

func main() {

    args := os.Args[1:]
    for _, url := range args {
        response, err := http.Get(url)
        check_err(err, "http GET error")
        response_body, err := ioutil.ReadAll(response.Body)
        response.Body.Close()
        check_err(err, "reading error")
        fmt.Printf("%s ", response_body) 
    }

}

func check_err(err error, estr string) {
    if err != nil {
        fmt.Fprintf(os.Stderr, "Fetch: %s %s ", estr, err)
        os.Exit(-2)
    }
}
```

Notes -

*   Package - **net**
    
*   Sub-package - net/http
    
*   Step 1 = Get URL by response, err := http.Get(url)
    
*   Step 2 = Put the response.Body in io.ReadAll() buffer and print the buffer
    
*   A client must close the body by calling response.Body.Close()
    
*   Notice using Printf instead of Print that uses %v verb, It will display response in numbers
    

#### 1.6. Fetching URLs Concurrently



```
// this program will use channels to simultaneously fetch the
// https response of the sites provided as an input

package main

import (
    "fmt"
    "io"
    "io/ioutil"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {

    start := time.Now()
    args := os.Args[1:]

    ch := make(chan string)

    for _, _url := range args {
        url := add_prefix(_url)
        go fetch(url, ch) // start a goroutine
    }

    for range args {
        fmt.Println(<-ch) // receive from channel ch
    }

    fmt.Printf("%.2f elapsed: \n", time.Since(start).Seconds())
}

func fetch(url string, ch chan<- string) {

    start := time.Now()
    response, err := http.Get(url)

    if err != nil {
        ch <- fmt.Sprint(err) // passing err to channel
        return
    }

    nbytes, err := io.Copy(ioutil.Discard, response.Body)

    response.Body.Close()

    if err != nil {
        ch <- fmt.Sprintf("while reading %s: %v", url, err)
        return
    }

    secs := time.Since(start).Seconds()

    ch <- fmt.Sprintf("%.2f  %7d  %s", secs, nbytes, url)
}

func add_prefix(url string) string {
    if strings.HasPrefix(url, "http://") {
        return url
    } else {
        return "http://" + url
    }
}
```

Notes -

*   **goroutine**: A concurrent execution function
    
*   **channel**: A communication mechanism that allows one goroutine to pass values of some type to another goroutine
    
*   How to create a **goroutine:** go _function\_call()_
    
*   How to create a **channel:** _channel\_name_:= make(chan _type)_
    
*   How to write channel argument in a function definition: _channel\_name_ chan<- _type_
    
*   Pass _value_ to channel ch: ch < - _value_
    
*   Fetch value from channel: < - ch
    



```
// A server which displays http response of the given URL

package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "sync"
)

var mu sync.Mutex
var count int
var url string

func main() {
    _url := os.Args[1]
    url = addPrefix(_url)
    http.HandleFunc("/sam", handler)   // each request calls handler, also increments hit count
    http.HandleFunc("/count", counter) // write to URL/count
    log.Fatal(http.ListenAndServe("localhost:8000", nil))
}

func handler(w http.ResponseWriter, r *http.Request) {
    mu.Lock()
    count++
    mu.Unlock()

    response, err := http.Get(url)
    errCheck(err)
    data, _ := ioutil.ReadAll(response.Body)
    response.Body.Close()
    fmt.Fprintf(w, "URL = %q\n", r.URL.Path)
    fmt.Fprintf(w, "Data = %s\n", data)
}

func counter(w http.ResponseWriter, r *http.Request) {
    mu.Lock()
    fmt.Fprintf(w, "Hit count : %d\n", count)
    mu.Unlock()
}

func errCheck(err error) {
    if err != nil {
        fmt.Printf("Fetch: Error %d", err)
    }
}

func addPrefix(url string) string {
    if strings.HasPrefix(url, "http://") {
        return url
    } else {
        return "http://" + url
    }
}
```

Notes -

*   Mutex -
    
    *   Package - sync
        
    *   type - sync.Mutex
        
    *   Use
        
        *   var mu sync.Mutex
            
        *   mu.Lock()
            
        *   mu.Unlock()
            
*   http
    
    *   http.HandleFunction(“/”, _handler\_func\_name_)
        
    *   http.ListenAndServe(“LocalHost:8000”, nil)
        

#### Uncategorized



*   **Variable Declarations**
    

1.  `s := ” ”` // Most compact but it can only be used within a function, not for package-level var
    
2.  `var s string` // ok, default init
    
3.  `var s = ” ”` // Rarely used, except when declaring multiple variables
    
4.  `var s string = ” ”` // form is explicit about the variable’s type
    
5.  `var i, j, k int` // int, int, int
    
6.  `var b, f, s = true, 2.3, "four"` // bool, float64, string
    

In practice generally, you should use one of the first two forms. And specify initializations only when you want to tell the compiler that the initial values are important

* * *

*   **Verbs**
    

1.  %d decimal integer
    
2.  %x, %o, %b integer in hexadecimal, octal, binary
    
3.  %f, %g, %e floating-point number: 3.141593 3.141592653589793 3.141593e+00
    
4.  %t boolean: true or false %c rune (Unicode code point)
    
5.  %s string
    
6.  %q quoted string "abc" or rune 'c'
    
7.  %v any value in a natural format
    
8.  %T type of any value
    
9.  %% literal percent sign (no operand)
    

*   **Go Program Structure**
    

1.  ```
    Put Hello World Program Here
    ```
    
2.  Functions and other package-level entities may be declared in any order. No function Prototype needed like C/C++
    
3.  **Control Flow**
    
    1.  if-else
        
    2.  for loop
        
    3.  switch case
        

**Switch Case**

```
switch coinflip() {
  case "heads":
    heads++
  case "tails":
    tails++
  default:
    fmt.Println("Landed on the edge")
}
```

1.  The cases are evaluated from top to bottom so the first matching one is executed
    
2.  The switch does not need an operand. It just lists the cases, each of which is a boolean expression
    

**Named types**

```
type Point struct {
  X, Y int
}

Usage:
var p Point
```

1.  A type declaration makes it possible to give a name to an existing type
    
2.  Since struct types are often long, they are almost always named
    

**Pointers**

1.  No pointer arithmetic
    
2.  & operator yields the address of a variable
    
3.  \* operator retrieves the variable the pointer is referring to
    

**Methods and Interfaces**

1.  Statements are grouped into functions for isolation and reuse
    
2.  A method is a function associated with a named type
    
3.  Interfaces are abstract types that let us treat different concrete types in the same way based on what methods they have and not how they represented or implemented
    

**Packages**

1.  Go comes with an extensive standard library of useful packages
    
2.  Programming is often more about using exiting packages than about writing the original code of our own
    
3.  Index of standard library packages - [https://golang.org/pkg](https://golang.org/pkg)
    
4.  Packages contributed by the community - [https://godoc.org/](https://godoc.org/)
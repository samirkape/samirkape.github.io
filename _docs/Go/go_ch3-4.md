---
 title:  "Chapter 3 & 4"
 description: "From The Go Programming Language"
---

- [Some Notes](#some-notes)
- [Composite Data Types](#composite-data-types)
  - [4.1 Arrays](#41-arrays)
- [4.2 Slices](#42-slices)
  - [4.2.1 The append Function](#421-the-append-function)
- [4.3 Maps](#43-maps)
- [4.4 Structs](#44-structs)
  - [4.4.1 Struct Literals and Comparsion](#441-struct-literals-and-comparsion)
- [4.5 JSON](#45-json)
  - [4.6 HTML Template](#46-html-template)
- [3.0 Basic Data Types](#30-basic-data-types)
  - [3.1 Integers](#31-integers)
  - [3.2. Floating-Point Numbers](#32-floating-point-numbers)
  - [3.4 Booleans](#34-booleans)
  - [3.5 Strings](#35-strings)
    - [3.5.1 String Literals](#351-string-literals)
    - [3.5.2 Unicode](#352-unicode)
    - [3.5.4 Strings and Byte Slices](#354-strings-and-byte-slices)
    - [3.5.5. Conversions between Strings and Numbers](#355-conversions-between-strings-and-numbers)
  - [3.6 Constants](#36-constants)

### Some Notes

1.  For reference types such as _pointers_ and _channels_, == checks for _**reference identity**_ that is whether two entities refer to the same entity
    
2.  Built-in **no\_of\_elements\_copied = copy(dst, src)** function copies the elements of two slices if they are of the same type. Works with a natural flow dst = src
    
3.  var and initialization does not come in the same statement
    
4.  Variadic function
    
    ```
    func main() {
       sum_it := {1, 2, 3, 4, 5}
       s := sum(sum_it...)
    }
    
    func sum(val ...int) int {
      s := 0
      for i := 0; i < len(sum); i++ {
        s += val[i]
      } 
      return s
    }
    ```
    

* * *

For converting from a string to a byte slice, `string -> []byte`:

```
[]byte(str)
```

For converting an array to a slice, `[20]byte -> []byte`:

```
arr[:]
```

For copying a string to an array, `string -> [20]byte`:

```
copy(arr[:], str)
```

Same as above, but explicitly converting the string to a slice first:

```
copy(arr[:], []byte(str))
```

* * *

*   The built-in `copy` function only copies **to** a slice, **from** a slice.
    
*   Arrays are "the underlying data", while slices are "a viewport into underlying data".
    
*   Using `[:]` makes an array qualify as a slice.
    
*   A string does not qualify as a slice that can be copied **to**, but it qualifies as a slice that can be copied **from** (strings are immutable).
    
*   If the string is too long, a `copy` will only copy the part of the string that fits.
    

* * *

This code:

```
var arr [20]byte
copy(arr[:], "abc")
fmt.Printf("array: %v (%T)\n", arr, arr)
```

...gives the following output:

```
array: [97 98 99 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0] ([20]uint8)
```

* * *

If we iterate over a string in for loop, the range function returns each character as a rune

* * *

*   %#v
    

\# adverb causes Printf’s %v verb to display values in a form similar to Go syntax. For struct values, this form includes the name of each field


```
fmt.Printf(“%#v”, w)
output:
main.Wheel{Circle:main.Circle{Center:main.Point{X:8, Y:8}, Radius:5}, Spokes:20}
```

*   %+v
    

when printing structs, the plus flag (`%+v`) adds field names

### Composite Data Types



1.  Arrays
    
2.  Slices
    
3.  Maps
    
4.  Structs
    

*   Arrays and structs are aggregate types; their values are concatenations of other values in memory
    
*   Arrays – Homogeneous ( All values are of the same type )
    
*   Structs – Heterogeneous
    
*   Both **arrays and structs** are **fixed-size data types**
    
*   In contrast **slices and maps** are **dynamic data structures**, which means **they grow as needed**
    

#### 4.1 Arrays



In Go, the most common purpose of an array is to hold place for the slice

An array is a fixed-length sequence of zero or more elements of a particular type.

Because of their fixed-length, arrays are **rarely used directly** in Go

Slices which can grow and shrink are much more versatile

The built-in function **len( )** returns the number of elements in the array

By default, the elements of a new array variable are **initially set to the zero value** for the element type, which is 0 for numbers

Array Literal – Initialize the array with a list of values

```
var q [3]int = [3]int{1, 2, 3}
var r [3]int = [3]int{1, 2}
fmt.Println(r[2]) // "0"
```

In an array literal, if an ellipsis ‘‘...’’ appears in place of the length, the array length is determined by the number of initializers. The definition of q can be simplified to

```
q := [...]int{1, 2, 3}
fmt.Printf("%T\n", q) // "[3]int"
```

If an array’s element type is comparable then the **array type is comparable** too, so we may directly compare two arrays of that type using the == operator

*   Array Type and Size should be the same
    

```
a := [2]int{1, 2}
b := [...]int{1, 2}
c := [2]int{1, 3}
fmt.Println(a == b, a == c, b == c) // "true false false"
d := [3]int{1, 2}
fmt.Println(a == d) // compile error: cannot compare [2]int == [3]int
```

When a function is called copy of each argument is assigned to the corresponding parameter. So the function receives a copy of arguments not original. Passing arrays in this way can be inefficient, any change that function makes to the array element does not affect the original array.

In this regard, Go treats arrays like any other variable type, **this behaviour is different from the languages which implicitly pass array by reference.**

Ofcourse, we can pass by reference explicitly,

```
func zero(ptr *[32]byte){
  for i := range ptr{
  ptr[i] = 0
  }
}
```

### 4.2 Slices



Slices represent variable-length sequences whose elements all have the same type. In short, slice describes a piece of an array

A slice type is written \[ \]T where,

*   All elements have type T
    
*   It looks like an array type without a size
    

Arrays and slices are intimately connected

**A slice is a lightweight data structure that gives access to the subsequent ( or perhaps all ) elements of an array**

* * *

A slice has three components,

*   A Pointer – Points to the first element of an array that is reachable through the slice, which is not necessarily the array’s first element
    
*   A Length – Number of slice elements ( Can’t exceed a capacity ) **len( )**
    
*   A Capacity – The number of elements between the start of the slice and the end of the underlying array **cap( )**
    

**Since a slice contains a pointer to an element of an array, passing a slice to a function permits the function to modify the elements of an underlying array. In other words, passing a slice creates an** _**alias**_ **for the underlying array.**

* * *

How to **compare** two slices?

*   Unlike an array, we can’t compare slices by ==
    
*   We can compare byte slices by using highly optimized **bytes.Equal**
    

Because slice elements are indirect, a fixed slice value may contain different elements at different times as the contents of the underlying array are modified. Because a hash table such as Go’s map type makes only shallow copies of its keys, it requires that equality for each key remain the same throughout the lifetime of the hash table. Deep equivalence would thus make slices **unsuitable for use as map keys**

**The only legal slice comparison is against nil. Because the zero value of the slice type is nil**

Other than comparing equal to nil, We can also pass nil to the function expecting a slice and it is perfectly safe.

* * *

We can create a slice using a built-in **make** function.

*   make(\[ \]T, len) – Slice is a view of the entire array
    
*   make(\[ \]T, len, cap) – Slice is a view of first len elements but its capacity includes cap
    

* * *

*   Slice Operations
    

```
slice = myappend(slice[:], 6) // if the slice is a pointer, 
                              // myappend is permitted to directly modify its contents
                              // But getting it returned has its reason listed below
```

We can’t assume when the append function will reallocate the given slice to fit the new element into the given slice. If it reallocates then it definitely **does not refer to the same slice you had passed** to append. Hence we have to return the result of the append function.

*   Updating the slice variable is required when you are **modifying the length or capacity** of the slice
    

or make it refer to a different underlying array. To use slices correctly it is important to bear in mind that the slice’s **pointer, length, and capacity** are not indirect and required to be updated by assignment. Hence, in this respect slices are not pure reference type but the aggregate type like below

```
type IntSlice struct {
  ptr *int
  len, cap int
}
```

* * *

#### 4.2.1 The append Function



The built-in append function appends items to slice

```
var runes []rune
str := "Hello world"
for _, element := range str {
  runes = append(runes, element)
} 
```

By using `append()` We can append multiple elements at once:

```
var x []int
x = append(x, 1, 2, 3)
x = append(x, 1, 2)
x = append(x, x...) // append the slice to x [1,2,3,1,2, 1,2,3,1,2]
```

### 4.3 Maps



A hash table is an ordered collection of a key/value pair in which, **all keys are distinct**

Regardless of hash table size, values can be updated, retrieved, or removed using a **constant number of key comparisons** on average

* * *

In Go, a map is a reference to the hash table and can be written as,

```
map[K]V
where,
K and V are types of its key and value
```

A key **can be compared using == operator** so that the map can test whether a given key is equal to one already within it.

*   How to create a map?
    

```
m := make(map[string]int) // Can be said as "mapping from strings to ints"
```

```
Using a map literal
m := map[string]int{
  "alice": 25,
  "bob": 25
}

this is equivalent to,

m := make(map[string]int)
m["alice"] = 25
m["bob"] = 25
```

* * *

*   How to access a map?
    

Using subscript notation

```
fmt.Println(m["alice"])
```

Accessing a map using subscript always yields a value, if a key does not exist then the corresponding 0 value of the type will be returned

* * *

*   How to delete a key?
    

Using built-in _delete()_ function

```
delete(m, "alice")
```

* * *

All of these operations are safe even if the given element is not present in the map. A map lookup using a key that isn’t present returns the zero value for its type

```
m := map[string]int{
  "alice": 25,
  "bob": 25
}
m["charlie"] = m["charlie"] + 1 // this will work. Value of m["charlie"] will be 1 
```

*   What are the operations that can be performed on a map?
    

The shorthand operations `x += y` and `x++` are allowed on the map.

```
m["charlie"] += 1
```

```
m["charlie"]++
```

*   Keep in mind that the map variable is not a variable and we cannot take its address.
    

```
add += &m["charlie"]  // Compile error
```

Why we can’t take the address of a map element?

_Growing a map might cause rehashing of existing elements into new storage locations thus potentially invalidating address._

* * *

*   How to access a map using loop
    

```
for key, value := range m {
  fmt.Println("Name",key)
  fmt.Println("Age",value)
}
```

_The order of the map iteration is unspecified and different implementation might use a different hash function leading to different ordering_

*   Zero value of a type map is **nil**
    

```
var m map[int]int
fmt.Println(m == nil) //true
```

Since a map is a composite data structure you must allocate memory to use it.

* * *

Can we compare two maps directly?

No! Unlike arrays, we can’t compare two maps. Only comparing with the nil is allowed.

The solution to this problem is writing a loop

* * *

The value type of a map can be a composite type such as a map or slice

### 4.4 Structs



Let’s look at the employee data

```
type Employee struct {
  ID int
  Name string
  Address string
  DoB time.Time
  Position string
  Salary string
  ManagerID int 
}
type samir Employee
```

*   How to access individual elements?
    

Using “. (dot) notation.

*   What is a zero value of a struct?
    

The zero value for the struct is composed of zero values of each of its fields.

*   Where would you use an _empty struct?_
    

One of the examples is maps where keys are important and values can be ignored. So instead of making values of certain data type which needs memory, we can use empty structs

```
seen := make(map[string]struct{}) // set of strings
// ...
if _, ok := seen[s]; !ok {
seen[s] = struct{}{}  // Saving space 
// ...first time seeing s...
}
```

#### 4.4.1 Struct Literals and Comparsion



Struct literal specifies values of struct fields

Two forms of struct literal?

*   The first form requires that a value be specified for every field and in the right order. It is better to use this for small structs.
    

```
type Point struct { X, Y int }
p := Point{1, 2}
```

*   This form is more often used in which a struct value initialized by some or more elements specified
    

**Comparison**

*   If all the fields of a struct are comparable, the struct itself is comparable
    
*   We can compare structs with == operator
    

```
type Point struct{ X, Y int }
p := Point{1, 2}
q := Point{2, 1}
fmt.Println(p.X == q.X && p.Y == q.Y) // "false"
fmt.Println(p == q) // "false"
```

*   Comparable struct types, like other comparable types, may be used as the key type of a map.
    

```
type address struct {
  hostname string
  port int
}
hits := make(map[address]int)
hits[address{"golang.org", 443}]++
```

### 4.5 JSON



How to encode?

```
type Movie struct {
	Title  string
	Year   int  `json:"released"`
	Color  bool `json: "color,omitempty"`
	Actors []string
}

var movies = []Movie{
	{Title: "Casanova", Year: 2005, Color: true, Actors: []string{"Heath Ledger"}},
	{Title: "Casablanca", Year: 1942, Color: false, Actors: []string{"Humphrey Bogart", "Ingrid Bergman"}},
}
```

What is marshelling?

Converting a Go data structure like movies to JSON is called marshaling. It is done by json.Marshal

```
data, err := json.Marshal(movies)
if err != nil {
log.Fatalf("JSON marshaling failed: %s", err)
}
fmt.Printf("%s\n", data)
```

Syntax: Marshal produces a byte slice containing a very long string with no extraneous white space

\[\]byte = json.Marshal

What is decoding?

#### 4.6 HTML Template



*   What is a template?
    

A template is a string enclosed in a double {{}} braces called actions

*   What do actions do?
    

Each action contains an expression in the template language, a simple but powerful notation for printing values, selecting struct fields, calling functions and methods, expressing control flow such as if-else statements and range loops, and instantiating other templates. A simple template string is shown below:

```
const templ = `{{.TotalCount}} issues:
     {{range .Items}}----------------------------------------
     Number: {{.Number}}
     User:   {{.User.Login}}
     Title:  {{.Title | printf "%.64s"}}
     Age:    {{.CreatedAt | daysAgo}} days
     {{end}}`
```

1.  The {{.TotalCount}} action expands to the value of the TotalCount field
    

2\. The {{range .Items}} and {{end}} actions create a loop, so the text between them is expanded multiple times, with dot bound to successive elements of Items.

*   How to produce an output with templates?
    

1.  We must parse a template into a suitable internal representation.
    
2.  Execute it on specific inputs
    

*   How to create the above templ
    

```
report, err := template.New("report").
         Funcs(template.FuncMap{"daysAgo": daysAgo}).Parse(templ)
     if err != nil {
         log.Fatal(err)
}
//in handler
if err := report.Execute(w, result); err != nil {
	log.Fatal(err)
}

// template.New creates and returns a template;
// Funcs adds daysAgo to the set of functions accessible within this template, then returns that template
// finally, Parse is called on the result.
```

* * *

```
var issueList = template.Must(template.New("issuelist").Parse(`
<h1>{{.TotalCount}} issues</h1>
<table>
<tr style='text-align: left'>
  <th>#</th>
  <th>State</th>
  <th>User</th>
  <th>Title</th>
</tr>
{{range .Items}}
<tr>
  <td><a href='{{.HTMLURL}}'>{{.Number}}</td>
  <td>{{.State}}</td>
  <td><a href='{{.User.HTMLURL}}'>{{.User.Login}}</a></td>
  <td><a href='{{.HTMLURL}}'>{{.Title}}</a></td>
</tr>
{{end}}
</table>
`))

// in handler
if err := issueList.Execute(w, result); err != nil {
	log.Fatal(err)
}
```

![](https://samirkape.atlassian.net/wiki/download/attachments/120520717/Screenshot%202021-04-07%20at%209.24.42%20AM.tiff?api=v2)

* * *

### 3.0 Basic Data Types



Go’s types fall into 4 categories

1.  Basic Data Types
    
    1.  Numbers
        
    2.  Strings
        
    3.  Booleans
        
2.  Aggregate Data types
    
    1.  Array
        
    2.  Struct
        
3.  References
    
    1.  Pointers
        
    2.  Slices
        
    3.  Maps
        
    4.  Channels
        
    5.  Functions
        
4.  Interfaces
    

* * *

All values of basic type—booleans, numbers, and strings—are comparable, meaning that two values of the same type may be compared using the == and != operators.

|     |     |
| --- | --- |
| &   | bit wise AND |
| \|  | bit wise OR |
| ^   | bit wise XOR |
| &^  | bit clear (AND NOT) |

var x uint8 = 1<<1 | 1<<5 = 0010 0010

var y uint8 = 1<<1 | 1<<2 = 0000 0110

Always use unsigned when you are playing with bit patterns. Because, right shifts of signed numbers fill the vacated bits with copies of the sign bit

#### 3.1 Integers



Go’s numeric data type includes several sizes of integers, floating-point numbers, complex numbers

Go provides both signed and unsigned integer arithmetic

For signed ints Go has,

1.  int8
    
2.  int16
    
3.  int32
    
4.  int64
    

For unsigned ints,

1.  uint8
    
2.  uint16
    
3.  uint32
    
4.  uint64
    

Along with those, there are also two more types, **int, and uint** that are the natural or most efficient sizes for integers. Both these have the same size, either 32 or 64 bit ( Compiler dependent )

* * *

Type **rune** is a synonym for **int32** and conventionally indicates that,

– The value is Unicode code point, hence these two names can be used interchangeably

Similarly, type **byte** is a synonym for **uint8**

– The value is a piece of raw data rather than a small numeric quantity

Regardless of their sizes, no two types can be compared without explicit conversion. Like **int** and **int32**

have the same sizes, but they are not the same for Go

* * *

Go’s binary operators for arithmetic, logic, and comparison are listed here in order of decreasing precedence left to right

|     |     |     |     |
| --- | --- | --- | --- |
| \*  | /   | %   | <<  |
| \>> | &   | &^  | +   |
| \-  | \|  | ^   | \== |
| !=  | <   | <=  | \>  |
| \>= | &&  | \|  |     |

% can only be used with integers

**Why you should not use unsigned for reverse loops**

```
medals := []string{"gold", "silver", "bronze"}
for i := len(medals) - 1; i >= 0; i-- {
fmt.Println(medals[i]) // "bronze", "silver", "gold"
}
```

If len returned an unsigned number, then i too would be a uint, and the condition i >= 0 would always be true by definition. After the third iteration, in which i == 0, the i-- statement would cause i to become not −1, but the maximum uint value (for example, 264−1), and the evaluation of medals\[i\] would fail at run time, or panic, by attempting to access an element outside the bounds of the slice.

* * *

Runes can be printed with %c or %q if the quotes are required

```
ascii := 'a'
unicode := 'D'
newline := '\n'
fmt.Printf("%d %[1]c %[1]q\n", ascii) // "97 a 'a'"
fmt.Printf("%d %[1]c %[1]q\n", unicode) // "22269 D 'D'"
fmt.Printf("%d %[1]q\n", newline) // "10 '\n'"
```

#### 3.2. Floating-Point Numbers



A **float32** provides approximately **six decimal digits of precision**,  
whereas a **float64** provides about **15 digits**;

**float64 should be preferred for most purposes** because float32 computations accumulate error rapidly unless one is quite careful, and the smallest positive integer that cannot be exactly represented as a float32 is not large

* * *

The function **math.IsNaN** tests **whether its argument is a not-a-number value**

**math.NaN** returns such value

**Any comparison with NaN always yields false**

#### 3.4 Booleans



The conditions in if and for statements are booleans, and comparison operators like == and < produce a boolean result.

Boolean values can be combined with the && (AND) and || (OR) operators, which have short circuit behavior

#### 3.5 Strings



**A string is an immutable sequence of bytes.**

Strings are conventionally interpreted as UTF-8-encoded sequence of Unicode code points ( runes )

The built-in **len( )** function returns the **number of bytes in a string**, not runes

And indexing operation string\[ i \] returns an ith byte of a string

Attempting to access a byte outside this range results in a panic:

The ith byte of string not necessarily ith character of a string

The i-th byte of a string is not necessarily the i-th character of a string, because the UTF-8 encoding of a non-ASCII code point requires two or more bytes

```
str := "hello, world"
str[len(i)] – Panic: index out of range

str[i:j] - Yeilds a new string with bytes of the original string. 
Starting at index i and continuing up to, but not including j 

str[0:5] is not "hello,"
str[0:5] is "hello"
------------------------------
Either or both of the operands may be ommited, in which case, the default values of 0
0 for start 
len(str) for end are assumed

e.g

str[:5] = "hello"
str[7:] = "world"
str[:] = "hello, world"

-------------------------------
```

**\+ operator makes a new string by concatenating two strings**

Also, **strings can be compared using comparison operators** like == and <. **The comparison is done byte by byte** so the result is natural lexicographical ordering

What does immutability mean?

*   It is safe for two copies of a string to share the same underlying memory. Making it cheap to copy strings of any length
    
*   Similarly, string str and its substring str\[7:\] may safely share the same data so substring operation is also cheap; No new memory allocated in either case.
    

##### 3.5.1 String Literals



**Raw string literals** –

A raw string literal is written **\`...\`**, using backquotes instead of double-quotes.

Within a raw string literal, no escape sequences are processed; the contents are taken literally, including backslashes and newlines, so a raw string literal may spread over several lines in the program source.

Raw string literals are a convenient way to write regular expressions, which tend to have lots of backslashes.

They are also useful for HTML templates, JSON literals, command usage messages, and the like, which often extend over multiple lines.

e.g

```
const GoUsage = `Go is a tool for managing Go source code.
Usage:
go command [arguments]
...`
```

##### 3.5.2 Unicode



**Unicode collects all of the characters in all of the world’s writing systems**, plus accents and other diacritical marks, control codes like tab and carriage return, and plenty of esoterica, and assigns each one a **standard number called a Unicode code point** or, in **Go terminology a rune**

Unicode version 8 defines code points for over 120,000 characters in well over 100 languages and scripts

In Go, _type_ to hold a single rune is int32 ( `type rune int32` )

Many string operations don’t require decoding.

*   HasPrefix() – We can test whether one string contains another as a prefix:
    
*   HasSuffix()
    
*   Contains()
    
*   Go’s **range** loop when applied to a string, performs **UTF-8 decoding implicitly**
    

Many Unicode characters are hard to type on a key board or to distinguish visually from similar-looking ones; some are even invisible

There are two forms( h = hex )

1.  `\uhhhh` for a 16-bit value
    
2.  `\Uhhhhhhhh` for a 32-bit value
    

```
import "unicode/utf8"

s := "Hello, (Japanese word here)"
fmt.Println(len(s)) // "13"
fmt.Println(utf8.RuneCountInString(s)) // "9"
```

If a slice of runes is converted to a string , it produces the concatenation of the UTF-8 encodings of each rune:

```
// "program" in Japanese katakana
s := "(Japanese words here)" 
fmt.Printf("% x\n", s) // "e3 83 97 e3 83 ad e3 82 b0 e3 83 a9 e3 83 a0"
r := []rune(s)
fmt.Printf("%x\n", r) // "[30d7 30ed 30b0 30e9 30e0]"
```

fmt.Println(string(r)) // `(Japanese words here)`

If the rune is invalid, the replacement character is substituted:

fmt.Println(string(1234567)) // "Question mark in black background”

##### 3.5.4 Strings and Byte Slices



Four standard packages are particularly important for the string manipulation

1.  bytes
    
2.  strings
    
3.  strconv
    
4.  unicode
    

2\. _strings_ package provides many functions for,

*   Searching
    
*   Replacing
    
*   Comparing
    
*   Splitting
    
*   Joining
    
*   Trimming, etc
    

1\. The _byte_ package has similar functions for manipulating slices of bytes of type \[ \]byte which share some properties with strings.

Because strings are immutable building up strings incrementally can involve lots of allocation and copying

3\. The _strconv_ package provides functions for converting booleans, integer, and floating-point values to and from their string representation

Also, functions for quoting and unquoting strings

4\. _unicode_ package provides functions like IsUpper( ) , IsLower( ), IsDigit( ), IsLetter( ) for classifying runes

Each function stated above takes a single rune as an argument and returns a boolean

```
Strings can be converted to byte slices and back again:
s := "abc"

b := []byte(s) // Allocates a new byte array holding a copy of the bytes
of s, and yields a slice that references the entirety of that array

s2 := string(b)
```

**Slices of Bytes –** Conceptually string contains an array of bytes that, once created is immutable. By contrast elements of byte slice can be freely modified

**String to Byte Conversion ( Line no 4. ) –**

`b := [ ]byte(s)` Allocates a new bytes array holding a copy of the bytes of s

The conversion from byte slice back to a string with `s2 := string(b)` also makes a copy, to ensure the immutability of the resulting string s2

##### 3.5.5. Conversions between Strings and Numbers



In addition to conversions between strings, runes, and bytes, it’s often necessary to convert between numeric values and their string representations. This is done with functions from the **strconv** package.

**To convert an integer to a string**,

1.  `fmt.Sprintf( “%d“, input_num )`
    
2.  `strconv.Itoa( num )`
    

```
x := 123
y := fmt.Sprintf("%d", x)
fmt.Println(y, strconv.Itoa(x)) // "123 123"
```

**To convert base of a given integer**

1.  `strconv.FormatInt( int64(num), base (e.g 2)`
    
2.  `fmt.Printf verbs ( %b, %d, %u, and %x )`
    

**To parse a string representing an integer**

1.  `strconv.ParseInt(str_num, base, no_of_bits)`
    
    1.  `strconv.ParseInt("123", 10, 64) // 64 for int64`
        
    2.  `strconv.ParseInt("123", 10, 16) // 16 for int16`
        
2.  `strconv.ParseUint(str_num)`
    
3.  `strconv.Atoi(str_num)`
    

Sometimes **fmt.Scanf** is useful for parsing input that consists of orderly mixtures of strings and numbers all on a single line, but it can be inflexible, especially when handling incomplete or irregular input.

#### 3.6 Constants



```
const (
a=1
b
c=2
d
)
fmt.Println(a, b, c, d) // "1 1 2 2"
```

**The Constant Generator iota**

A const declaration may use the Constant Generator iota, which used to create the sequence of related values without spelling out each one explicitly. In const declaration**, the value of iota begins at 0 and increments by one for each item in the sequence**

```
type Weekday int

const (
Sunday Weekday = iota
Monday
Tuesday
Wednesday
Thursday
Friday
Saturday
)
// this declares Sunday to be 0, Monday 1 and so on
```
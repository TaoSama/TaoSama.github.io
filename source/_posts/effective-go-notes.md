---
title: Notes, Effective Go
categories:
  - Doing
  - Go
  - 
tags:
  - 
  - 
date: 2017-08-04 17:52:10
toc: true

---
~~Last Modified: 2017-08-23 11:46:10~~

~~Hard to say, I finished Go, for about 20 days (except busy for 1 week and training for 1 week).~~

~~Such a long time, 1 week to learn a new language, having known a little about concurrency.~~

~~Not so bad.~~

### Acknowledge
Go is a new language. Although it borrows ideas from existing languages, it has unusual properties that make effective Go programs different in character from programs written in its relatives.
The notes below was written from the view of **a CPP programmer**.

* and a hello world

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World")
}
```

<!--- more --->

### Syntax
#### Basics
* function

```go
func swap(x, y string) (string, string) {
	return y, x
}
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```
* variable

```go
var c, python, java bool
var i, j int = 1, 2
var c, python, java = true, false, "no!"

k := 3 // only can be used inside function
```

* basic types

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128

// The int, uint, and uintptr types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. 
```

* var blocks

```go
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)
```

* zero values
Variables declared without an explicit initial value are given their zero value.
`0` for `numeric` types,
`false` for the `boolean` type, and
`""` (the empty string) for `strings`.

* type conversions

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
// more simply
i := 42
f := float64(i)
u := uint(f)
```

* type inference

```go
var i int
j := i // j is an int
3
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

* constants

```go
const Pi = 2.14
const Truth bool = true

// numeric constants are high-precision values.
const (
	Big = 1 << 100
	Small = Big >> 99
)
```

#### Flow control
* for 

```go
for i := 0; i < 10; i++ {
    // do something
}
for ; i < 10; {
    // do something
}

// C's `while` is spelled `for` in Go
i := 0
for i < 10 {
    // do something
}

// loop forever
for {
    // do something
}
```

* if

```go
if i < 10 {
    // do something
} else {
    // do something
}

if i < 10 {
    // do something
} else if i < 100 {
    // do something
}

// if with a short statement
// variables declared by the statement are only in scope until the end of the if.
if v := 1; v < 10 {
    // do something
}
```

* exercise-loops-and-functions.go

```go
package main

import (
	"fmt"
	"math"
)

func Sqrt(x float64) float64 {
	var cur float64 = x
	for nxt := 0.0; math.Abs(nxt - cur) > 1e-8;  {
		t := cur - (cur * cur - x) / (2 * cur)
		nxt = cur
		cur = t
	}
	return cur
}

func main() {
	fmt.Println(Sqrt(2))
}
```

* switch

```go
// a case body breaks automatically, unless it ends with a 'fallthrough' statement.
fmt.Print("Go runs on ")
switch os := runtime.GOOS; os {
case "darwin":
	fmt.Println("OS X.")
case "linux":
	fmt.Println("Linux.")
default:
	// freebsd, openbsd,
	// plan9, windows...
	fmt.Printf("%s.", os)
}
	
// switch with no condition
t := time.Now()
switch {
case t.Hour() < 12:
	fmt.Println("Good morning!")
case t.Hour() < 17:
	fmt.Println("Good afternoon.")
case t.Hour() < 24:
	fmt.Println("One more")
	fallthrough
default:
	fmt.Println("Good evening.")
}
```

* defer

```go
// deferred function calls are pushed onto a stack. 
// when a function returns, its deferred calls are executed in last-in-first-out order.
func main() {
	fmt.Println("counting")

	for i := 0; i < 3; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}
/*
counting
done
2
1
0
*/
```

#### More types
* pointers

```go
// the type *T is a pointer to a T value.
// its zero value is nil.
var p *int

i := 42
p = &i

fmt.Println(*p) // read i through the pointer p
*p = 21         // set i through the pointer p
```

* struct

```cpp
type Vertex struct {
	X int
	Y int
}

var v Vertex = Vertex{2, 3}  // v := VertexP{2, 3}
p := &v  // pointer to structs
v.x = 1e9
p.X = 1e9 // implicit conversion ???

// struct literals
var (
	v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
)
```

* array

```go
var prime [6]int = [6]int{2, 3, 5, 7, 11, 13}
```
* slices

```go
// slices are like references to arrays
var s []int = primes[1:4]

// array literal
[3]bool{true, true, false}
// slice literal
[]bool{true, true, false}

// slice defaults
// the default is zero for the low bound and the length of the slice for the high bound.
// these slice expressions are equivalent:
a[0:10]
a[:10]
a[0:]
a[:]

// nil slices
// A nil slice has a length and capacity of 0 and has no underlying array.
var s []int
fmt.Println(s, len(s), cap(s))
if s == nil {
	fmt.Println("nil!")
}
/*
[] 0 0
nil!
*/
```

* slice length and capacity

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)

	// Slice the slice to give it zero length.
	s = s[:0]
	printSlice(s)

	// Extend its length.
	s = s[:4]
	printSlice(s)


	// Drop its first two values.
	s = s[2:]
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
/*
len=6 cap=6 [2 3 5 7 11 13]
len=0 cap=6 []
len=4 cap=6 [2 3 5 7]
len=2 cap=4 [5 7]
*/
```

* creating a slice with make
slices can be created with the built-in make function; 
this is how you create dynamically-sized arrays.

```go
b := make([]int, 0, 5) // len(b)=0, cap(b)=5
```

* slices of slices

```go
board := [][]string{
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
}
for i := 0; i < len(board); i++ {
	fmt.Printf("%s\n", strings.Join(board[i], " "))
}
```

* apending to a slice

```go
// func append(s []T, vs ...T) []T
var s []int
s = append(s, 1)
s = append(s, 2, 3, 4)

// append slice
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
```

* range

```go
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}
for i, v := range pow {
	fmt.Printf("2**%d = %d\n", i, v)
}

// skip the index or value
pow := make([]int, 10)
for i := range pow {
	pow[i] = 1 << uint(i) // == 2**i
}
for _, value := range pow {
	fmt.Printf("%d\n", value)
}
```

* exercise-slices.go

```go
package main

import "golang.org/x/tour/pic"

func Pic(dx, dy int) [][]uint8 {
	var pic [][]uint8 = make([][]uint8, dy)
	for i := 0; i < dy; i++ {
		pic[i] = make([]uint8, dx)
		for j := 0; j < dx; j++ {
			pic[i][j] = uint8(i ^ j);	
		}
	}
	return pic
}

func main() {
	pic.Show(Pic)
}
```


* maps
The zero value of a map is nil. A nil map has no keys, nor can keys be added.

```go
type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex
m = make(map[string]Vertex)
m["Bell Labs"] = Vertex{
	40.68433, -74.39967,
}
fmt.Println(m["Bell Labs"])

// map literals
var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}

// If the top-level type is just a type name, you can omit it from the elements of the literal.
var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}

// mutating maps
// insert or update an element in map m:
m[key] = elem
// retrieve an element:
elem = m[key]
// delete an element:
delete(m, key)
// test that a key is present with a two-value assignment:
elem, ok = m[key]
```

* exercise-maps.go

```go
package main

import (
	"golang.org/x/tour/wc";
	"strings"
)

func WordCount(s string) map[string]int {
	word := strings.Split(s, " ")
	var mp map[string]int = make(map[string]int)
	for i := 0; i < len(word); i++ {
		mp[word[i]] += 1
	}
	return mp
}

func main() {
	wc.Test(WordCount)
}
```

* funtion values

```go
func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))

	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))
}
```

* closure

  a closure is a function value that references variables from outside its body. 
the function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

```go
// each closure is bound to its own sum variable.
func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 3; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
/*
0 0
1 -2
3 -6
*/
```

* function-closures.go

```go
package main

import "fmt"

// fibonacci is a function that returns
// a function that returns an int.
func fibonacci() func() int {
	var f0, f1 int = 0, 1
	return func() int {
		ret := f0
		f0, f1 = f1, f0 + f1
		return ret
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```

### Methods and interfaces
#### methods
* **go does not have classes**. However, you can define methods on types.
* a method is a **function** with a **special receiver argument**.
* the receiver appears in its own argument list **between the func keyword and the method name**

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

* declare a method on non-struct types

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	v.Scale(10)
	fmt.Println(v.Abs())
}

```

* In general, all methods on a given type should have either value or pointer receivers, but not a mixture of both.

#### interfaces
an interface type is defined as a set of method signatures.
a value of interface type can hold any value that implements those methods.

* interfaces are implemented implicitly
implicit interfaces **decouple** the definition of an interface from its implementation, which could then appear in any package without prearrangement.

* interface values
an interface value holds a value of a specific underlying concrete type.
it can be thought of as a tuple of a value and a concrete type: `(value, type)`

* interface values can be with nil underlying values.

```go
type I interface {
	M()
}

type T struct {
	S string
}

// This method means type T implements the interface I,
// but we don't need to explicitly declare that it does so.
func (t T) M() {
    if t == nil {  
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}

func main() {
	var i I
	describe(i)   // nil interface value -> run-time error

	var t *T
	i = t
	describe(i)  // nil receiver -> OK
	i.M()

	i = &T{"hello"}
	describe(i)
	i.M()
}

```

* empty interface
an empty interface may hold values of any type. (every type implements at least zero methods.)
empty interfaces are used by code that handles values of unknown type.
```go
var i interface{}
i = 42
i = "hello"
```

* type assertions

```go
// if i does not hold a type T, the statement will trigger a panic.
t := i.(T)

// if i does not hold a type T, ok will be false 
// and t will be the zero value of type T, and no panic occurs.
t, ok := i.(T)
```

* type switches

```go
package main

import "fmt"

func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
```

* Stringers and error

```go
// a Stringer is a type that can describe itself as a string.
type Stringer interface {
    String() string
}

// the error type is a built-in interface similar to fmt.Stringer
type error interface {
    Error() string
}

// for example, fmt.Println will call the two interfaces
```

* exercise-stringer.go

```go
package main

import "fmt"

type IPAddr [4]byte

// TODO: Add a "String() string" method to IPAddr.

func (t *IPAddr) String() string {
	return fmt.Sprintf("%v.%v.%v.%v", t[0], t[1], t[2], t[3])	
}

func main() {
	hosts := map[string]IPAddr{
		"loopback":  {127, 0, 0, 1},
		"googleDNS": {8, 8, 8, 8},
	}
	for name, ip := range hosts {
		fmt.Println(ip.String())
		fmt.Printf("%v: %v\n", name, ip)
	}
}
```

* exercise-errors.go

```go
package main

import (
	"fmt"
)

type ErrNegativeSqrt float64

func (e ErrNegativeSqrt) Error() string {
	return fmt.Sprintf("cannot Sqrt negative number: %v", float64(e))	
}

func Sqrt(x float64) (float64, error) {
	if x < 0 {
		return 0, ErrNegativeSqrt(x)	
	}
	z := 1.0
	for i := 0; i < 10; i++ {
		z = z - (z * z - x) / (2 * z), y
	}
	return z, nil
}

func main() {
	fmt.Println(Sqrt(2))
	fmt.Println(Sqrt(-2))
}
```

* readers
interface `io.Reader::Read`
```go
func (T) Read(b []byte) (n int, err error)
```
* exercise-reader.go

```go
package main

import "golang.org/x/tour/reader"

type MyReader struct{}

// TODO: Add a Read([]byte) (int, error) method to MyReader.
func (t MyReader) Read(b []byte) (int, error) {
	for i := 0; i < len(b); i++ {
		b[i] = 'A'	
	}
	return len(b), nil
}

func main() {
	reader.Validate(MyReader{})
}
```

* exercise-rot-reader.go

```go
package main

import (
	"io"
	"os"
	"strings"
)

type rot13Reader struct {
	r io.Reader
}

func (r rot13Reader) Read(b []byte) (n int, err error) {
	insideR := r.r
	if n, err = insideR.Read(b); err != nil {
		return	
	}
	for i := 0; i < n; i++ {
		if b[i] >= 'a' && b[i] <= 'z' {
			b[i] = (b[i] - 'a' + 13) % 26 + 'a'	
		} else if b[i] >= 'A' && b[i] <= 'Z' {
			b[i] = (b[i] - 'A' + 13) % 26 + 'A'	
		}
	}
	return
}

func main() {
	s := strings.NewReader("Lbh penpxrq gur pbqr!")
	r := rot13Reader{s}
	io.Copy(os.Stdout, &r)
}
```

* exercise-images.go

```go
package main

import (
	"golang.org/x/tour/pic"
	"image"
	"image/color"
)

/*
type Image interface {
    ColorModel() color.Model
    Bounds() Rectangle
    At(x, y int) color.Color
}
*/

type Image struct{
	Width, Height int
}

func (img Image) ColorModel() color.Model {
	return color.RGBAModel	
}

func (img Image) Bounds() image.Rectangle {
	return image.Rect(0, 0, img.Width, img.Height)
}

func (img Image) At(x, y int) color.Color {
	return color.RGBA{uint8(x * y), uint8((x + y) / 2), 100, 255}	
}

func main() {
	m := Image{100, 100}
	pic.ShowImage(m)
}
```

### Concurrency
#### goroutines
A goroutine is a lightweight thread managed by the Go runtime.
`go f(x, y, z)` starts a new goroutine running `f(x, y, z)`.
The **evaluation** of `f, x, y, and z` happens in the **current goroutine** and the **execution** of `f` happens in the **new goroutine**.
Goroutines run in the **same address space**, so access to shared memory must be synchronized. 

#### channels
Channels are a typed conduit(pipe) through which you can send and receive values with the channel operator, `<-`.
By default, **sends and receives block until the other side is ready**. This allows goroutines to **synchronize without explicit locks or condition variables**.
```go
// (The data flows in the direction of the arrow.)
// Like maps and slices, channels must be created before use:
ch := make(chan int)
ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and
           // assign value to v.
```

#### buffered Channels
Channels can be buffered. Provide the buffer length as the second argument to make to initialize a buffered channel:
`ch := make(chan int, 100)`
**Sends** to a buffered channel **block** only when the buffer is **full**. **Receives** **block** when the buffer is **empty**.

#### range and close
`v, ok := <-ch`
ok is false if there are no more values to receive and the channel is closed.
The loop `for i := range c` **receives values** from the channel repeatedly **until** it is **closed**.

**Note**: Only the sender should close a channel, never the receiver. Sending on a closed channel will cause a panic.

**Another note**: Channels aren't like files; you don't usually need to close them.
**Closing** is only necessary when the receiver must be told there are **no more values coming**, such as to **terminate** a `range` **loop**.

```go
func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Println(i)
	}
}
```

#### select
The `select` statement lets a goroutine wait on multiple communication operations.
A `select` blocks until one of its cases can run, then it executes that case. 
**It chooses one at random if multiple are ready**.

Use a `default` case to try a send or receive without blocking:

```go
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
		default:
            // receiving from c would block
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```

#### sync.Mutex
We've seen how **channels** are great for **communication among goroutines**.
But what if we don't need communication? What if we just want to make sure only one goroutine can access a variable at a time to avoid conflicts?
This concept is called **mutual exclusion**, and the conventional name for the data structure that provides it is **mutex**.
Go's standard library provides mutual exclusion with `sync.Mutex` and its two methods:
`Lock, Unlock`
```go
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mux.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mux.Unlock()
	return c.v[key]
}
```

* exercise-equivalent-binary-trees.go

```go
package main

import (
	"golang.org/x/tour/tree"

	"fmt"
)

// Walk walks the tree t sending all values
// from the tree to the channel ch.
func Walk(t *tree.Tree, ch chan int) {
	defer close(ch)
	var walker func(t *tree.Tree)
	walker = func(t *tree.Tree) {
		if t == nil {
			return
		}
		walker(t.Left)
		ch <- t.Value
		walker(t.Right)

	}
	walker(t)
}

// Same determines whether the trees
// t1 and t2 contain the same values.
func Same(t1, t2 *tree.Tree) bool {
	ch1 := make(chan int)
	ch2 := make(chan int)
	go Walk(t1, ch1)
	go Walk(t2, ch2)

	for x1 := range ch1 {
		x2, ok2 := <-ch2
		if !ok2 || x1 != x2 {
			return false
		}
	}
	return true
}

func main() {
	fmt.Println(Same(tree.New(1), tree.New(1)))
	fmt.Println(Same(tree.New(1), tree.New(2)))
}
```

* exercise-web-crawler.go

```go
package main

import (
	"fmt"
	"sync"
)

type Fetcher interface {
	// Fetch returns the body of URL and
	// a slice of URLs found on that page.
	Fetch(url string) (body string, urls []string, err error)
}

type Cache struct {
	visited map[string]bool	
	mtx sync.Mutex
}

type Response struct {
	url string
	body string	
}

// Crawl uses fetcher to recursively crawl
// pages starting with url, to a maximum of depth.
func Crawl(url string, depth int, fetcher Fetcher, result chan Response, cache Cache) {
	// TODO: Fetch URLs in parallel.
	// TODO: Don't fetch the same URL twice.
	// This implementation doesn't do either:
	defer close(result)
	if depth <= 0 {
		return
	}
	
	cache.mtx.Lock()
	if cache.visited[url] {
		cache.mtx.Unlock()
		return
	}
	cache.visited[url] = true
	cache.mtx.Unlock()
	
	body, urls, err := fetcher.Fetch(url)
	if err != nil {
		fmt.Println(err)
		return
	}
	
	result <- Response{url, body}
	// fmt.Printf("found: %s %q\n", url, body)
	
	for _, u := range urls {
		tempResult := make(chan Response)
		go Crawl(u, depth-1, fetcher, tempResult, cache)
		for re := range tempResult {
			result <- re
		}
	}
	return
}

func main() {
	result := make(chan Response)
	go Crawl("http://golang.org/", 4, fetcher, result, Cache{visited:make(map[string]bool)})
	
	for re := range result {
		fmt.Printf("found: %s %q\n", re.url, re.body)
	}
}

// fakeFetcher is Fetcher that returns canned results.
type fakeFetcher map[string]*fakeResult

type fakeResult struct {
	body string
	urls []string
}

func (f fakeFetcher) Fetch(url string) (string, []string, error) {
	if res, ok := f[url]; ok {
		return res.body, res.urls, nil
	}
	return "", nil, fmt.Errorf("not found: %s", url)
}

// fetcher is a populated fakeFetcher.
var fetcher = fakeFetcher{
	"http://golang.org/": &fakeResult{
		"The Go Programming Language",
		[]string{
			"http://golang.org/pkg/",
			"http://golang.org/cmd/",
		},
	},
	"http://golang.org/pkg/": &fakeResult{
		"Packages",
		[]string{
			"http://golang.org/",
			"http://golang.org/cmd/",
			"http://golang.org/pkg/fmt/",
			"http://golang.org/pkg/os/",
		},
	},
	"http://golang.org/pkg/fmt/": &fakeResult{
		"Package fmt",
		[]string{
			"http://golang.org/",
			"http://golang.org/pkg/",
		},
	},
	"http://golang.org/pkg/os/": &fakeResult{
		"Package os",
		[]string{
			"http://golang.org/",
			"http://golang.org/pkg/",
		},
	},
}
```



### Language specification
#### Formatting
* gofmt ?
* Indentation
  We use tabs for indentation and gofmt emits them by default. Use spaces only if you must.
* Line length
  Go has no line length limit. Don't worry about overflowing a punched card. If a line feels too long, wrap it and indent with an extra tab.
* Parentheses
  Go needs fewer parentheses than C and Java: control structures (if, for, switch) do not have parentheses in their syntax. Also, the operator precedence hierarchy is shorter and clearer, so `x<<8 + y<<16` means what the spacing implies, unlike in the other languages.

#### Commentary
* Go provides C-style `/* */` block comments and C++-style `//` line comments. 
* Line comments are the norm; 
* Block comments appear mostly as package comments, but are useful within an expression or to disable large swaths of code.

* **Every exported (capitalized) name in a program should have a doc comment**.
* Doc comments work best as complete sentences, which allow a wide variety of automated presentations. 
* The first sentence should be a **one-sentence summary** that **starts with the name being declared**.

#### Names
* Package names
  By convention, packages are given lower case, single-word names; there should be no need for underscores or mixedCaps. For example, the package in `src/encoding/base64` is imported as `"encoding/base64"` but has name base64, not `encoding_base64` and not `encodingBase64`.
* Clear and Concise Name
  ring.New
  once.Do
* Getters and Setters
If you have a field called owner (lower case, unexported), the getter method should be called Owner (upper case, exported), not GetOwner.
```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```
* Interface names
By convention, one-method interfaces are named by the method name plus an -er suffix or similar modification to construct an agent noun: Reader, Writer, Formatter, CloseNotifier etc.
* MixedCaps
Finally, the convention in Go is to use MixedCaps or mixedCaps rather than underscores to write multiword names.

#### Semicolons
* Like C, Go's formal grammar uses semicolons to terminate statements, but unlike in C, those semicolons do not appear in the source.
* If the last token before a newline is an identifier, the lexer always inserts a semicolon after the token. This could be summarized as, “if the newline comes after a token that could end a statement, insert a semicolon”.

```go
// identifiers
break continue fallthrough return ++ -- ) }
```

* One consequence of the semicolon insertion rules is that you **cannot** put the **opening brace** of a control structure (if, for, switch, or select) **on the next line**. 
  If you do, a semicolon will be inserted before the brace, which could cause unwanted effects.

```go
// right
if i < f() {
    g()
}

// wrong
if i < f() 
{
    g()
}
```

#### Redeclaration and reassignment
In a `:=` declaration a variable `v` may appear even if it has already been declared, provided:

* this declaration is in the same scope as the existing declaration of v (if v is already declared in an outer scope, the declaration will create a new variable),
* the corresponding value in the initialization is assignable to v, and
* there is at least one other variable in the declaration that is being declared a new.

```go
f, err := os.Open(name)
d, err := f.Stat()
```

#### Data
* Allocation with new
`new(T)` returns a pointer to a newly allocated zero value of type `T`.
* Allocation with make
`make(T, args)` serves a purpose different from `new(T)`. 
It creates slices, maps, and channels only, and it returns an **initialized (not zeroed)** value of type `T` (not `*T`). 
The reason for the distinction is that these three types represent, under the covers, references to data structures that must be initialized before use.

#### Arrays
There are major differences between the ways arrays work in Go and C. In Go,

* Arrays are values. Assigning one array to another copies all the elements.
* In particular, if you pass an array to a function, it will receive a copy of the array, not a pointer to it.
* The size of an array is part of its type. The types `[10]int` and `[20]int` are distinct.

The value property can be useful but also expensive; 
If you want C-like behavior and efficiency, you can **pass a pointer to the array**.
**But even this style isn't idiomatic Go. Use slices instead.**

#### The blank identifier
The blank identifier can be assigned or declared with any value of any type, with the value discarded harmlessly.

* Unused imports and variables

```go
import (
    "fmt"
    "io"
    "log"
    "os"
)

var _ = fmt.Printf // For debugging; delete when done. 
var _ io.Reader    // For debugging; delete when done. 

func main() {
    fd, err := os.Open("test.go")
    if err != nil {
        log.Fatal(err)
    }
    // TODO: use fd.
    _ = fd
}
```

* Import for side effect
`import _ "net/http/pprof"`
This form of import makes clear that the package is being imported for its side effects, because there is no other possible use of the package: in this file, it doesn't have a name. (If it did, and we didn't use that name, the compiler would reject the program.)

* Interface checks
If it's necessary only to ask whether a type implements an interface, without actually using the interface itself, perhaps as part of an error check, use the blank identifier to ignore the type-asserted value:
```go
if _, ok := val.(json.Marshaler); ok {
    fmt.Printf("value %v of type %T implements json.Marshaler\n", val, val)
}
```

One place this situation arises is when it is necessary to guarantee within the package implementing the type that it actually satisfies the interface. 
If a type—for example, `json.RawMessage` needs a custom JSON representation, it should implement json.Marshaler, but there are no static conversions that would cause the compiler to verify this automatically. 
If the type inadvertently fails to satisfy the interface, the JSON encoder will still work, but will not use the custom implementation. 
To guarantee that the implementation is correct, a global declaration using the blank identifier can be used in the package:
```go
var _ json.Marshaler = (*RawMessage)(nil)
```

#### Concurrency
concurrency: structuring a program as independently executing components. 
parallelism: executing calculations in parallel for efficiency on multiple CPUs. 

* Channels of channels
  // TODO

* Parallelization
Either run your job with environment variable `GOMAXPROCS` set to the number of cores to use or import the `runtime` package and call `runtime.GOMAXPROCS(NCPU)`. 
A helpful value might be `runtime.NumCPU()`, which reports the number of logical CPUs on the local machine.

* A leaky buffer
  // TODO

#### Errors
* Panic
Panic that in effect creates a run-time error that will stop the program.
It's always better to let things continue to run rather than taking down the whole program. One possible counterexample is during initialization: if the library truly cannot set itself up, it might be reasonable to panic, so to speak.

```go
var user = os.Getenv("USER")

func init() {
    if user == "" {
        panic("no value for $USER")
    }
}
```

* Recover
When panic is called, including implicitly for run-time errors such as indexing a slice out of bounds or failing a type assertion, it immediately stops execution of the current function and begins unwinding the stack of the goroutine, running any deferred functions along the way. 
If that unwinding reaches the top of the goroutine's stack, the program dies. However, it is possible to use the built-in function **recover to regain control of the goroutine and resume normal execution**.
One application of recover is to shut down a failing goroutine inside a server without killing the other executing goroutines.

```go
func server(workChan <-chan *Work) {
    for work := range workChan {
        go safelyDo(work)
    }
}

func safelyDo(work *Work) {
    defer func() {
        if err := recover(); err != nil {
            log.Println("work failed:", err)
        }
    }()
    do(work)
}
```

// TODO: the rest of contents...

#### A web server
Let's finish with a complete Go program, a web server.
This one is actually a kind of web re-server. Google provides a service at http://chart.apis.google.com that does automatic formatting of data into charts and graphs.
It's hard to use interactively, though, because you need to put the data into the URL as a query.
The program here provides a nicer interface to one form of data: given a short piece of text, it calls on the chart server to produce a QR code, a matrix of boxes that encode the text. That image can be grabbed with your cell phone's camera and interpreted as, for instance, a URL, saving you typing the URL into the phone's tiny keyboard.
Here's the complete program. 
```go
package main

import (
    "flag"
    "html/template"
    "log"
    "net/http"
)

var addr = flag.String("addr", ":1718", "http service address") // Q=17, R=18

var templ = template.Must(template.New("qr").Parse(templateStr))

func main() {
    flag.Parse()
    http.Handle("/", http.HandlerFunc(QR))
    err := http.ListenAndServe(*addr, nil)
    if err != nil {
        log.Fatal("ListenAndServe:", err)
    }
}

func QR(w http.ResponseWriter, req *http.Request) {
    templ.Execute(w, req.FormValue("s"))
}

const templateStr = `
<html>
<head>
<title>QR Link Generator</title>
</head>
<body>
{{if .}}
<img src="http://chart.apis.google.com/chart?chs=300x300&cht=qr&choe=UTF-8&chl={{.}}" />
<br>
{{.}}
<br>
<br>
{{end}}
<form action="/" name=f method="GET"><input maxLength=1024 size=70
name=s value="" title="Text to QR Encode"><input type=submit
value="Show QR" name=qr>
</form>
</body>
</html>
`
```


### Reference
[Go Official Site](https://golang.org/)
[A Tour of Go](https://tour.golang.org/)
[Effective Go](https://www.gitbook.com/book/bingohuang/effective-go-zh-en/details)
[The Go Programming Language Specification](https://golang.org/ref/spec)

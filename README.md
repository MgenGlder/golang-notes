# golang-notes
Notes for golang

# Go Tour (Notes)
#go-tour

* When exporting functions, make sure to capitalize them.
* “Unexported” names aren’t accessible from outside of the package.
* Use “factored” exports instead of single line exports as below 👇🏾
  * ```
    import (
    "fmt"
    "math"
    )
  ```
- A name is exported if it begins with a capital letter.
  - When importing a package, you can refer only to its exported names.
- A function may return any number of results.
  - They can also be named, but this can harm readability.
- Recall that `:=` is short hand, called “short assignment”.
- Variable declarations may be “factored” into blocks, as with import statements. See below 👇🏾
```go
    var (
    ToBe   bool       = false
    MaxInt uint64     = 1<<64 - 1
    z      complex128 = cmplx.Sqrt(-5 + 12i)
```
- Recall, variables declared without an explicit value are given their *zero value*. 
  - 0 for numeric types
  - false for boolean types
  - “” (empty string) for strings.
- Type Conversions
  ```go
  var i int = 42
  var f float64 = float64(i)
  var u uint = uint(f)
  ```
- Or, more simply
  ```go
  i := 42
  f := float64(i)
  u := uint(f)
  ```

- There is no “while” loop in Go. You still use “for” 👇🏾
  ```go
  for sum < 1000 {
  	sum += sum
  }
  ```

- If you omit the condition, it loops forever.
  - For and if statements are allowed to start with a short assignment statement to execute before the condition
    ```go
    if v := math.Pow(x, n); v < lim {
    	return v
    }
    return lim
    ```

  - The `v` variable can only be used in the scope of the if statement _or_ the associated `else` statement, if there is one.
- Switch statements
  ```go
  switch os := runtime.GOOS; os {
  	case “Darwin”:
  	  fmt.Println(“OS X”)
  	case “linux”: 
   	 fmt.Println(“Linux”)
  	default:
   	 fmt.Printf(“%s.\n”, os)
  }
  ```

- A switch without a condition is the same as `switch true` .
  - This can be a clean way to write long if-then-else statements.
    ```go
    switch {
    case t.Hour() < 12:
    //do stuff
    case t.Hour() < 17:
    //do other stuff
    default
    //do default stuff
    }
    ```

- A **defer** statement defers the execution of a function until the surrounding function returns.
  - However, the deferred call’s arguments are evaluated immediately.
    ```go
    func main() {
    	defer fmt.Println("world")
    	fmt.Println("hello")
    }
    ```

  - **NOTE** ‼️ - Deferred calls are pushed onto a stack and executed in last-in-first-out order.
- Go has pointers, a pointer holds a memory address of a value.
  - The type `*T` is a pointer to a `T` value. It’s zero value is `nil`.
    - `var p *int`
  - The `&` operator generates a pointer to its operand
    - `i := 42`
    - `p = &i`
  - The `*` operator denotes the pointers underlying value.
    ```go
    fmt.Println(*p) // read i through the pointer p (dereference)
    *p = 21 // set i through the pointer p (indirect)
    ```
    - This is known as “dereferencing” and “indirecting”
    - Indirection and dereferencing mean the same thing
- A `struct` is a collection of fields
  ```go
  type Vertex struct {
  	X int
  	Y int
  }
  func main() {
  	fmt.Println(Vertex{1, 2})
  }
  ```
  - Struct fields are accessed using a dot.
    - `v.X`
  - Struct fields can be accessed through a struct pointer.
    ```go
    func main() {
    	v := Vertex{1, 2}
    	p := &v
    	p.X = 1e9
    	fmt.Println(v)
    }
    ```
  - To access the field `X` of a struct when we have the struct pointer `p` we could write `(*p).X`. However, that notation is cumbersome, so the language permits us instead to write just `p.X`, without the explicit dereference.
- Struct Literals
  - A struct literal denotes a newly allocated struct value by listing the values of its fields.
  - You can list just a subset of fields by using the `Name:` syntax. (And the order of named fields is irrelevant.)
  - The special prefix `&` returns a pointer to the struct value.

```go
var (
	v1 = Vertex{1, 2}
	v2 = Vertex{X: 1} // Y is default 0
	v3 = Vertex{} // X and Y are 0
	p = &Vertex{1, 2} // return the pointer to the new struct
)
```
- Arrays
  - The type `[n]T` is an array of `n` values of type `T`.
  - The expression
    `var a [10]int`
    declares a variable `a` as an array of ten integers.
  - An arrays length is part of its type, so arrays cannot be resized. This seems limiting, but don’t worry; Go provides a convenient way of working with arrays.
  - `primes := [6]int{2,3,5,7,11,13}`
- Slices
  - An array has a fixed size. A slice, on the other hand, is a dynamically-sized, flexible view into the elements of an array. In practice, slices are much more common than arrays.
  - `[]T` is a slice with the elements of type `T`.
  - A slice is formed by specifying 2 indices, a low and high bound, separated by a colon:
    `a[low : high]`
    - Includes first element and excludes the last.

  ```go
  func main() {
    primes := [6]int{2,3,5,7,11,13}
  
    var s []int = primes[1:4]
    fmt.Println(s)
  }
  ```
  - Slices don’t store any data, they just describe a section of an underlying array.
    - Slice literals are like an array literal without the length.
      - `[]bool{true, true, false}`
  - When slicing, you may omit the high or low bounds to user their defaults instead.
    `var a [10]int`
  - These slices are equivalent:
    ```go
    a[0:10]
    a[:10]
    a[0:]
    a[:]
    ```
  - A slice’s capacity is the number of elements in the underlying array.
  - A slice’s length is the number of elements it contains.
  - Slices can be created with the built-in `make` function; this is how you create dynamically-sized arrays.
    - `a := make([]int, 5) // len(a)=5`
    - To specify the capacity, add a third argument.
      - `a := make([]int, 0, 5) // len 0, cap 5`
  - Slices can contain any type, including other slices.
    ```go
    func main() {
    board := [][]string {
    	[]string{"_", "_", "_"},
    	[]string{"_", "_", "_"}
    }
    ```

  - Appending to a slice
    - Go provides a built-in `append()` that allows you to do this.
    - `append(s, 1) //appends 1 to slice of ints`
  - Range
    - Range is a form of a `for` loop to iterate over a slice or map.
    - When ranging over a slice, two values are returned for each iteration. The first is the index, and the second is a copy of the element at that index.
      ```go
      func main(){
      	for i, v := range pow {
      	    fmt.Printf("2**%d = %d\n", i, v)
        }
      }
      ```
    - Remember, you can skip the index or value by assigning it to `_`.
      ```go
      for i, _ := range pow
      for _, value := range pow
      ```
    - If you only want the index, you can omit the second variable.
      `for i := range pow`
  - Creating a 2d nested slice
    ```go
    func Pic(dx, dy int) [][]uint8 {
    	slice := make([][]uint8, dy)
    
    	for i := range slice {
    		slice[i] = make([]uint8, dx)
    	}
    
    	return slice
    }
    ```


- Maps - A map maps keys to values. The zero value of a map is nil
  - `make` returns a map of the given type, initialized and ready to use. 👇🏾
    ```go
    type Vertex struct {
    	Lat, Long float64
    }
    
    var m map[string]Vertex
    
    func main() {
        m = make(map[string]Vertex)
    	m["Bell Labs"] = Vertex{
    		40.68433, -74.39967,
    	}
    	fmt.Println(m["Bell Labs"])
    }
    
    ```
  - Map literals are like struct literals, but the keys are required.
    ```go
    type Vertex struct {
    	Lat, Long, float64
    }
    
    var m = map[string]Vertex{
    	"Bell Labs": Vertex{
    		40.68433, -74.39967,
    	},
        "Google": Vertex{
    		37.42202, -122.08408,
    	},
    }
    ```
  - If the top level type is just a type name, you can omit it from the elements of the literal:
    ```go
    var m = map[string]Vertex{
    	"Bell Labs": {40.68433, -74.39967},
    	"Google":    {37.42202, -122.08408},
    }
    ```

  - Mutating maps
    - `m[key] = elem`
    - `elem = m[key]`
    - `delete(m, key)`
    - `elem, ok = m[key] // test that key is present`
  - Loop over words and count the number of words
    ```go
    func WordCount(s string) map[string]int {
    fields := strings.Fields(s)
    holderMap := make(map[string]int)
    
    for _, v := range fields {
    	_, ok := holderMap[v]
    	if ok {
    		holderMap[v] += 1
    	} else {
    	holderMap[v] = 1	
    	}
    }
    	fmt.Println(holderMap)
    
    	return holderMap
    }	
    ```

- Functions are values too. They can be passed around just like other values.
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

- Function closures - go functions may also be closures, a closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is “bound” to the variables.
  ```go
  func adder() func(int) int {
  sum := 0
  return func(x int) int {
  	sum += x
  	return sum
  }
  }
  
  func main() {
    pos, neg := adder(), adder()
    for i := 0; i < 10; i++ {
  	  fmt.Println(
  		  pos(i),
  		  neg(-2*i),
  	  )
    }
  }
  ```
  - An incredibly terse way to do fibonacci sequence in go with closures
    ```go
    // fibonacci is a function that returns
    // a function that returns an int.
    func fibonacci() func() int {
    	x, y := 0, 1
        return func() int {
    		f := x
    		x, y = y, f+y
    
    		return f
    	}
    }
    func main() {
    	f := fibonacci()
    	for i := 0; i < 10; i++ {
    		fmt.Println(f())
        }
    }
    ```
- Methods
  - Go does not have classes, however, you can define methods on types.
  - A method is a function with a special _receiver_ argument.
    ```go
    type Vertex struct {
    	X, Y float64
    }
    func (v Vertex) Abs() float64 {
    	return math.Sqrt(v.X*x.Y + v.Y*v.Y)
    }
    func main() {
        v := Vertex{3, 4}
    	fmt.Println(v.Abs())
    }
    ```

  - Methods a re functions with a receiver argument.
  - You can declare a method on non-struct types, too.
    - In this example we see a numeric type `MyFloat` with an `Abs` method.
    - You can only declare a method with a receiver whose type is defined in the same package as the method. You cannot declare a method with a receiver whose type is defined in another package (which includes the built-in types such as `int`).
      ```go
      type MyFloat float64
      
      func (f MyFloat) Abs() float64 {
          if f < 0 {
      		return float64(-f)
          }
          return float64(f)
      }
      
      func main() {
      	f := MyFloat(-math.Sqrt2)
      	fmt.Println(f.Abs())
      }      
      ```

- You can declare methods with pointer receivers. This means the receiver type has the liter syntax `*T` for some type `T`.
  - Methods with pointer receivers can modify the value to which the receiver points. Since methods often need to modify their receiver, pointer receivers are more common than value receivers.
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
- You can rewrite the same methods as functions if you want: 👇🏾
  ```go
  type Vertex struct {
    X, Y float64
  }
  
  func Abs(v Vertex) float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }
  
  func Scale(v *Vertex, f float64) {
    v.X = v.X * f
    v.Y = v.Y * f
  }
  
  func main() {
    v := Vertex{3, 4}
    Scale(&v, 10)
    fmt.Println(Abs(v))
  }
  ```
- Note that methods with pointer references take either a value or a pointer as the receiver when they are called:
  ```go
  var v Vertex
  v.Scale(5) // OK
  p := &v
  p.Scale(10) // OK
  ```
- For the statement `v.Scale(5)`, even though `v` is a value and not a pointer, the method with the pointer receiver is called automatically. That is, as a convenience, Go interprets the statement `v.Scale(5)` as `(&v).Scale(5)` since the `Scale` method has a pointer reference.
  - The same happens in the reverse case for value receivers.
  - `p.Abs()` is interpreted as `(*p).Abs()`.
- Choosing a value or pointer receiver
  - 2 reasons to use a pointer receiver
    - The first is so that the method can modify the value that its receiver points to.
    - The second is to avoid copying the value on each method call. This can be more efficient if the receiver is a large struct, for example.
    - Both `Scale` and `Abs` are methods with receiver type `*Vertex`, even though the Abs method needn’t modify its receiver.
    - In general, all methods on a given type should have either value or pointer receivers, not not a mixture of both.
- Interfaces
  - An *interface type* is defined as a set of method signatures.
  - A value of interface type can hold any value that implements those methods.
    ```go
    type Abser interface {
    	Abs() float64
    }
    
    func main() {
        var a Abser
    	f := MyFloat(-	math.Sqrt2) // Works, Abs defined below on the struct
        v := Vertex{3, 4} /// Works
    ...
    ```
  - With interfaces in Go, there’s no explicit declaration of intent- no “implements” keyword.
  - Under the hood, interface values can be thought of as a tuple of a value and a concrete type: 
    - `(value, type)`
  - An interface value holds a value of a specific underlying concrete type.
  - Calling a method on a nil interface is a run-time error.
  - The interface type that specifies zero methods is known as the *empty interface*.
    `interface{}`
    - An empty interface may hold values of any type. (Every type implements at least zero methods.)
    - Empty interfaces are used by code that handles values of unknown types.
- Type assertions
  - A type assertion provides access to an interface value’s underlying concrete values 👇🏾
  - `t := i.(T)`
    - This statement asserts that the interface value `i` holds the concrete type `T` and assigns the underlying `T` value to the variate `t`.
      - If `i` doesn’t hold a `T`, the statement will trigger a panic.
    - To *test* whether an interface value holds a specific type, a type assertion can return two values: the underlying value and a boolean value that reports whether the assertion succeeded.
      `t, ok := i.(T)`
      - This will not trigger a panic if it doesn’t contain a 	`T`, but `ok` will be set to `false`.
- Type switch is a construct that permits several type assertions in series.
  - The type switch is like a regular switch statement, but the cases in a type switch specify types (not values), and those values are compared against the type of the value held by the given interface value. 👇🏾
    ```go
    switch v := i.(type) {
    case T:
    // here v has type T
    case S:
    // here v has type S
    default:
    // no match; here v has the same type as i
    }
    ```
- Stringers - one of the most ubiquitous interfaces, defined by the `fmt` package.
  - A `Stringer` is a type that can describe itself as a string. The `fmt` package (and many others) look for this interface to print values.
    ```go
    type Stringer interface {
    	String() string
    }
    ```

  - If you want to convert an object to a `Stringer` type, check out this example below 👇🏾
    ```go
    package main
    
    import (
    "fmt"
    "strconv"
    )
    
    type IPAddr [4]byte
    
    // TODO: Add a "String() string" method to IPAddr.
    
    func (ip IPAddr) String() string {
    	var s string 
    	for i, v := range ip {
    		s += strconv.Itoa(int(v))
    		if i != 3 {
    			s += "."
    		}
    	}
    	return s
    }
    
    func main() {
    	hosts := map[string]IPAddr{
    		"loopback":  {127, 0, 0, 1},
    		"googleDNS": {8, 8, 8, 8},
    	}
    	for name, ip := range hosts {
    		fmt.Printf("%v: %v\n", name, ip)
    	}
    }
    ```

- Generic Functions - Go functions can be written to work on multiple types using type parameters. The type parameters of a function appear between brackets, before the function’s arguments.
  - `func Index[T comparable](s []T, x T) int`
    - The declaration means that `s` is a slice of any type `T` that fulfills the built-in constraint `comparable`. `x` is also a value of the same type.
    - `comparable` is a useful constraint that makes it possible to use the `==` and `!=` operators on values of the type. In this example, we use it to compare a value to all slice elements until a match is found. This `Index` function works for any type that supports comparison.
- Generic Types - in addition to generic functions, go also supports generic types. A type can be parameterized with a type parameter, which could be useful for implementing generic data structures.
  ```go
  package main
  
  // List represents a singly-linked list that holds
  // values of any type.
  type List[T any] struct {
    next *List[T]
    val  T
  }
  
  func main() {
  }
  ```
- Goroutines - a lightweight thread managed by the Go runtime.
  - `go f(x, y, z)`
    starts a new grouting running 
    `f(x, y, z)`
  - The evaluation of `f`,`x`,`y`, and `z` happens in the current goroutine and the execution of `f` happens in the new coroutine.
  - Goroutines run in the same address space, so access to shared memory must be synchronized. The `sync` package provides useful primitives, although you won’t need them much in Go as there are other primitives.
- Channels - channels are a typed conduit through which you can send and receive values with the channel operator `<-`.
  `ch <- v // Send v to channel ch.`
  `v := <- ch // Receive from ch and assign value to v.`
  - The data flows in the direction of the arrow.
  - Like maps and slices, channels must be created before use:
    `ch := make(chan int)`
  - Channels can be *buffered*. Provide the buffer length as the second argument to `make` to initialize a buffered channel:
    `ch := make(chan int, 100)`
  - A sender the `close` a channel to indicate that no more values will be sent. Receivers can test whether a channel has been closed by assigning a second parameter to the receive expression after:
    `v, ok := <- ch`.
  - If `ok` is `false` there are no more values to receive and the channel is closed.
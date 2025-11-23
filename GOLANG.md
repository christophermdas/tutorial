# GOLANG.md
*A complete, end-to-end Go (Golang) tutorial — beginner → advanced → modern Go 1.18–1.23+, with real-world backend/CLI/service idioms.*

> **How to use this tutorial:** Read sequentially. After each chapter, do the mini‑exercises in a scratch repo. Treat the examples as patterns you’ll reuse in real services.

---

## Table of Contents

- **Part 1: Go Fundamentals**
  - Chapter 1.1: What is Go + Why it Exists
  - Chapter 1.2: Setup, Toolchain, Modules, Workspaces, Runtime
  - Chapter 1.3: Your First Program
  - Chapter 1.4: Variables & Types
  - Chapter 1.5: Operators & Control Flow
  - Chapter 1.6: Functions
  - Chapter 1.7: Pointers & Value Semantics
  - Chapter 1.8: Arrays, Slices, and Strings
- **Part 2: Structs, Methods, and Interfaces**
  - Chapter 2.1: Structs
  - Chapter 2.2: Methods & Receivers
  - Chapter 2.3: Interfaces
  - Chapter 2.4: Encapsulation & Package Design
  - Chapter 2.5: Errors (Deep)
- **Part 3: Generics, Collections, and Functional-ish Patterns**
  - Chapter 3.1: Generics (Go 1.18+)
  - Chapter 3.2: Maps
  - Chapter 3.3: Advanced Slice Patterns
- **Part 4: Concurrency & Go Runtime**
  - Chapter 4.1: Goroutines
  - Chapter 4.2: Channels
  - Chapter 4.3: Context
  - Chapter 4.4: `sync` Package Essentials
  - Chapter 4.5: Memory Model Basics
- **Part 5: I/O, Networking, and Standard Library Power**
  - Chapter 5.1: Files & Directories (`os`, `io`, `bufio`)
  - Chapter 5.2: JSON & Encoding
  - Chapter 5.3: HTTP in Go (`net/http`)
  - Chapter 5.4: Testing
- **Part 6: Modern Go Evolution (Go 1.18 → Go 1.23+)**
  - Chapter 6.1: Release Matrix Overview
  - Chapter 6.2: Go 1.18
  - Chapter 6.3: Go 1.19
  - Chapter 6.4: Go 1.20
  - Chapter 6.5: Go 1.21
  - Chapter 6.6: Go 1.22
  - Chapter 6.7: Go 1.23+
- **Part 7: Tooling, Performance, and Deployment**
  - Chapter 7.1: Go Tooling
  - Chapter 7.2: Modules Deep Dive
  - Chapter 7.3: Profiling & Tracing
  - Chapter 7.4: Build & Release
- **Part 8: Ecosystem & Best Practices**
  - Chapter 8.1: Project Structure
  - Chapter 8.2: Code Style
  - Chapter 8.3: Common Architectural Patterns in Go

---

# Part 1: Go Fundamentals

## Chapter 1.1: What is Go + Why it Exists

### Section 1.1.1: What is Go?
Go is a **statically typed, compiled, garbage‑collected** programming language created at Google. It sits in a sweet spot between:
- **systems languages** (C/C++) → performance/control, but complex and unsafe
- **managed languages** (Java/C#) → safety/tools, but heavier runtimes/verbosity
- **dynamic languages** (Python/JS/Ruby) → rapid development, but slower and runtime errors

Go tries to deliver **fast development and fast execution** with a small, readable language.

### Section 1.1.2: Why Go exists (design goals)
Go was built to solve large‑scale engineering pain:
1. **Slow builds** in huge C/C++/Java monorepos.
2. **Complexity tax** from feature‑heavy languages.
3. **Concurrency pain** (threads are expensive, hard to reason about).
4. **Deployment friction** (VMs, big artifacts).

So Go emphasizes:
- **simple syntax + small spec** → easy to read, easy to onboard teams
- **fast compilation** → tight edit/compile/test loop
- **goroutines + channels** → concurrency that feels natural
- **single static binary** → easy shipping and ops

### Section 1.1.3: Compiled vs interpreted
**Compiled (Go)**:
- source → machine code → run
- catches many errors before execution
- very fast startup
- distribution is easy (copy one binary)

**Interpreted (or JIT)**:
- source → VM/interpreter/JIT → run
- errors discovered later
- startup depends on VM
- needs runtime installed

| Property | Compiled (Go) | Interpreted/JIT (Python/JS/Java) |
|---|---|---|
| Error detection | mostly compile‑time | mostly runtime |
| Startup | fast | depends |
| Distribution | single binary | source + runtime |
| Performance | predictable | varies |

### Section 1.1.4: Go toolchain overview
Go’s “standard toolchain” is part of the language experience:
- `go run` — compile+run quickly for local dev
- `go build` — produce binaries
- `go test` — tests, benchmarks, fuzzing, race detector
- `go fmt` / `gofmt` — canonical formatting (everyone’s code looks the same)
- `go vet` — static analysis for suspicious patterns
- `go mod` / `go work` — dependency + workspace management

A key cultural point: **formatting and basic static checks are non‑negotiable**. Go assumes tools enforce consistency so humans focus on logic.

### Section 1.1.5: Packages, modules, workspace
- **Package:** a directory of `.go` files with the same `package` name. Packages are the unit of compilation and encapsulation.
- **Module:** a versioned collection of packages defined by a `go.mod`. Modules are the unit of dependency management.
- **Workspace (Go 1.18+):** a `go.work` file that allows multiple modules to be developed together locally. Great for microservices + shared libs.

### Section 1.1.6: Go runtime (GC + scheduler) — high level
Go ships with a runtime that provides:
- **Garbage Collection (GC):** automatically frees unused heap allocations. Go’s GC is concurrent, meaning it runs alongside your program to reduce pause times.
- **Scheduler:** maps potentially millions of goroutines onto a smaller set of OS threads.
- **Network poller:** efficient I/O multiplexing so goroutines can park waiting for sockets without blocking OS threads.

**G‑M‑P mental model (simplified):**
```
G = goroutine (your lightweight tasks)
M = OS thread (actual kernel thread)
P = processor slot (holds runnable queue)

   P0 ----> runq: G1 G2 G3
    |                 ^
    v                 |
   M0 ---------------/
```
Think: **many Gs**, **few Ms**, **Ps are tokens** that let an M execute Go code.

**Mini‑exercises**
1. Write 3 differences between Go and Java in philosophy (not syntax).
2. Explain why a small language spec matters for large teams.

---

## Chapter 1.2: Setup, Toolchain, Modules, Workspaces, Runtime

### Section 1.2.1: Installing latest Go
Install Go from official distribution. Verify:
```bash
go version
```
You want Go 1.23+ to follow all modern examples.

### Section 1.2.2: GOPATH vs modules
Historically, Go required code under `$GOPATH/src` and downloaded deps into GOPATH. That model struggled for reproducible builds and versioning.

**Modules** (default since Go 1.16) fix this:
- deps are versioned in `go.mod`/`go.sum`
- code can live anywhere
- builds are reproducible

**Rule:** Use modules unless you’re maintaining legacy GOPATH projects.

### Section 1.2.3: VS Code / GoLand setup tips
- enable format‑on‑save (`gofmt`)
- use `gopls` language server
- enable “organize imports on save”
- turn on “staticcheck” in editor if available

### Section 1.2.4: First module: `go mod init`
```bash
mkdir hello-go && cd hello-go
go mod init example.com/hello-go
```
`go.mod` defines module path and minimum Go version.

### Section 1.2.5: Workspace mode
When working on multiple modules together:
```bash
go work init ./service-a ./service-b
go work use ./shared-lib
```
Now imports resolve to your local copies without `replace` spam.

**ASCII build/run pipeline**
```
.go files
   |
   v
go fmt   -> canonical formatting
   |
   v
go test  -> compile + tests (+race/+bench/+fuzz)
   |
   v
go build -> binaries
   |
   v
deploy
```

**Mini‑exercises**
1. Create two modules and a workspace; import one from the other.
2. Add a Makefile target `test-race` that runs `go test -race ./...`.

---

## Chapter 1.3: Your First Program

### Section 1.3.1: `package main` and `main()`
Executables are built from package `main`. The runtime looks for `func main()`.

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, Go!")
}
```

### Section 1.3.2: Run and build
```bash
go run .
go build -o hello .
./hello
```

### Section 1.3.3: Basic CLI args
`os.Args` holds raw command‑line args.

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	fmt.Println("program:", os.Args[0])
	fmt.Println("args:", os.Args[1:])
}
```

**Mini‑exercises**
1. Parse `-name` using `flag` and print a greeting.
2. Exit with status code 1 if no args are provided.

---

## Chapter 1.4: Variables & Types

### Section 1.4.1: Declarations and zero values
Go variables always have a type and a default **zero value**.

```go
var a int    // 0
var b = 10   // inferred int
c := "hi"    // short decl, only inside funcs
```

Zero values are useful because they simplify initialization and reduce “null checking”.

| Type | Zero value |
|---|---|
| numbers | `0` |
| bool | `false` |
| string | `""` |
| pointers, maps, slices, chans, funcs, interfaces | `nil` |

### Section 1.4.2: Basic types
| Category | Types | Theory |
|---|---|---|
| booleans | `bool` | true/false |
| signed ints | `int`, `int8`, `int16`, `int32`, `int64` | `int` matches CPU word size |
| unsigned | `uint`, `uint8`(byte), `uint16`, `uint32`, `uint64`, `uintptr` | use for bit patterns/IDs |
| floats | `float32`, `float64` | IEEE‑754 |
| complex | `complex64`, `complex128` | each part float |
| text | `string`, `rune`, `byte` | UTF‑8 strings; `rune` is code point |

### Section 1.4.3: Constants and `iota`
Constants are compile‑time values.

```go
const Pi = 3.14159

const (
	Red = iota
	Green
	Blue
)
```

`iota` is a counter reset each `const` block — used for enums, bit masks.

### Section 1.4.4: Type conversions
Go requires explicit conversion. This prevents hidden precision loss.

```go
var x int32 = 10
var y int64 = int64(x)

f := float64(y)
```

### Section 1.4.5: Key pitfalls
- `len(str)` counts **bytes**, not characters.
- `int` size differs between 32‑bit and 64‑bit systems.
- avoid `float32` unless memory bandwidth matters.

**Mini‑exercises**
1. Use `iota` to build bit flags for permissions.
2. Convert `[]byte` to `string` and explain allocation cost.

---

## Chapter 1.5: Operators & Control Flow

### Section 1.5.1: Operators
Go includes standard arithmetic, comparison, logical and bitwise operators.

| Group | Operators | Notes |
|---|---|---|
| arithmetic | `+ - * / %` | integer division truncates |
| comparison | `== != < <= > >=` | structs/arrays comparable if fields comparable |
| logical | `&& || !` | short‑circuit |
| bitwise | `& | ^ << >> &^` | `&^` is bit clear |

### Section 1.5.2: `if` with short statement
```go
if n := len(name); n == 0 {
	return errors.New("empty name")
}
```
The declared `n` is scoped to the `if` block — a nice way to keep variables tight.

### Section 1.5.3: `switch`
Switch is more powerful than many languages:
- can switch on expressions
- can do type switches
- no fallthrough by default (safer)

```go
switch day {
case "Sat", "Sun":
	fmt.Println("weekend")
default:
	fmt.Println("weekday")
}
```

### Section 1.5.4: `for`
Go has one loop keyword.

```go
for i := 0; i < 3; i++ {} // classic

for cond {}              // while style

for { break }            // infinite
```

### Section 1.5.5: `range`
`range` iterates over slices, arrays, maps, strings, channels, and iterators (1.23+).

```go
for i, v := range []int{10, 20} {
	fmt.Println(i, v)
}
```

### Section 1.5.6: `defer`
`defer` schedules a call to run at function exit (LIFO order). Great for cleanup.

```go
func readFile(p string) ([]byte, error) {
	f, err := os.Open(p)
	if err != nil { return nil, err }
	defer f.Close()
	return io.ReadAll(f)
}
```

### Section 1.5.7: `panic` and `recover` (intro)
`panic` aborts normal control flow — meant for programmer errors, not business errors.
`recover` can intercept a panic inside a deferred function.

```go
func safe(fn func()) (err error) {
	defer func() {
		if r := recover(); r != nil {
			err = fmt.Errorf("panic: %v", r)
		}
	}()
	fn()
	return nil
}
```

**Mini‑exercises**
1. Measure function time using `defer`.
2. Write a `switch` that groups HTTP status codes by class.

---

## Chapter 1.6: Functions

### Section 1.6.1: Multiple returns
Multiple returns are core to Go’s error handling.

```go
func div(a, b int) (int, error) {
	if b == 0 {
		return 0, fmt.Errorf("divide by zero")
	}
	return a / b, nil
}
```

### Section 1.6.2: Named returns
Named results can improve clarity or enable bare `return`, but avoid overusing bare returns in long functions.

```go
func minMax(nums []int) (min, max int) {
	min, max = nums[0], nums[0]
	for _, n := range nums[1:] {
		if n < min { min = n }
		if n > max { max = n }
	}
	return
}
```

### Section 1.6.3: Variadic functions
Variadic params allow “zero or more” arguments.

```go
func sum(nums ...int) int {
	t := 0
	for _, n := range nums { t += n }
	return t
}
```

### Section 1.6.4: Closures
Closures capture variables from outer scope.

```go
func makeAdder(delta int) func(int) int {
	return func(x int) int { return x + delta }
}
```

### Section 1.6.5: Error conventions
- return errors as last value
- name errors precisely
- wrap to add context
- avoid exceptions for flow

```go
u, err := repo.Get(id)
if err != nil {
	return User{}, fmt.Errorf("get user %d: %w", id, err)
}
```

**Mini‑exercises**
1. Implement `mapInts`.
2. Implement `retry(attempts, fn)` with exponential backoff.

---

## Chapter 1.7: Pointers & Value Semantics

### Section 1.7.1: Pointer basics
Pointers hold addresses. `&` takes address; `*` dereferences.

```go
x := 10
p := &x
*p = 20
fmt.Println(x) // 20
```

### Section 1.7.2: Value semantics in Go
Everything is passed **by value**. When you pass a pointer, you’re passing a value that points elsewhere.

### Section 1.7.3: Value vs pointer parameters
Use **value** for:
- small structs
- immutability
- copies are okay

Use **pointer** for:
- mutation
- avoiding big copies
- representing optional values (nil)

### Section 1.7.4: Escape analysis intuition
The compiler decides stack vs heap. A value “escapes” to heap if it must outlive its stack frame. Don’t optimize based on guesses; use pprof.

**Mini‑exercise**
- Write `func inc(p *int)` and explain which memory changes.

---

## Chapter 1.8: Arrays, Slices, and Strings

### Section 1.8.1: Arrays vs slices
Arrays are fixed-size values; slices are dynamic descriptors over arrays.

| Feature | Array `[N]T` | Slice `[]T` |
|---|---|---|
| size | fixed, part of type | dynamic |
| passing | copies | cheap |
| usage | rare | ubiquitous |

### Section 1.8.2: Slice internals
A slice is:
- pointer to backing array
- length
- capacity

This matters because slicing shares backing arrays.

```go
s := []int{1,2,3,4}
sub := s[1:3] // shares backing array
sub[0] = 99
fmt.Println(s) // [1 99 3 4]
```

### Section 1.8.3: `append` and reallocation
Appending may allocate a new array if capacity is exceeded. This is why preallocation helps performance.

```go
s := make([]int, 0, 10)
for i := 0; i < 10; i++ {
	s = append(s, i)
}
```

### Section 1.8.4: Strings in Go
Strings are immutable byte sequences (UTF‑8 by convention). Converting to `[]byte` or `[]rune` allocates.

```go
str := "héllo"
b := []byte(str) // bytes
r := []rune(str) // runes (code points)
```

### Section 1.8.5: Common pitfalls
- appending to a subslice can overwrite original if capacity overlaps
- slicing large arrays keeps whole array alive in memory
- `for range string` yields runes, not bytes

**Mini‑exercises**
1. Implement `removeAt` without preserving order.
2. Write `truncateRunes(s, n)` safely for UTF‑8.

---

# Part 2: Structs, Methods, and Interfaces

## Chapter 2.1: Structs

### Section 2.1.1: Struct basics
Structs are Go’s way to group fields into composite types. They’re value types.

```go
type User struct {
	ID int
	Name string
}
```

### Section 2.1.2: Tags and reflection
Tags are string metadata used by reflection-based packages like `encoding/json`.

```go
type User struct {
	ID int `json:"id" db:"id"`
	Email string `json:"email"`
}
```

### Section 2.1.3: Embedding (composition)
Embedding promotes fields/methods, encouraging composition over inheritance.

```go
type Audit struct{ CreatedAt time.Time }
type Order struct{
	ID int
	Audit
}
```

### Section 2.1.4: Anonymous structs
Useful for local scaffolding or ad‑hoc JSON.

```go
resp := struct {
	Data any `json:"data"`
}{Data: "ok"}
```

**Mini‑exercises**
1. Define `Product` with tags.
2. Embed `Audit` in multiple domain types.

---

## Chapter 2.2: Methods & Receivers

### Section 2.2.1: Methods
Methods attach behavior to types.

```go
type Counter struct{ n int }
func (c *Counter) Inc() { c.n++ }
func (c Counter) Value() int { return c.n }
```

### Section 2.2.2: Value vs pointer receivers (theory)
Receivers affect:
- **mutability** (only pointer receivers can mutate)
- **method sets** (what satisfies interfaces)
- **copy cost**

A good rule: if **any** method needs pointer, make **all** methods pointer receivers for consistency.

### Section 2.2.3: Method sets (important for interfaces)
- `T` has methods with receiver `T`
- `*T` has methods with receiver `T` and `*T`

**Mini‑exercises**
1. Add `Reset` to Counter.
2. Explain interface satisfaction for `T` vs `*T`.

---

## Chapter 2.3: Interfaces

### Section 2.3.1: Implicit implementation
Interfaces specify behavior; types satisfy them automatically if they implement methods.

```go
type Stringer interface{ String() string }

type Product struct{ Name string }
func (p Product) String() string { return p.Name }
```

### Section 2.3.2: Interface composition
Small interfaces compose into larger ones.

```go
type ReadWriter interface{
	io.Reader
	io.Writer
}
```

### Section 2.3.3: `any` / empty interface
`any` can hold values of any type. Use where you truly need dynamic behavior (e.g., JSON). Prefer generics for type-safe collections.

### Section 2.3.4: Assertions and switches
Type assertions let you “extract” the dynamic type.

```go
var x any = 10
v, ok := x.(int)
```

### Section 2.3.5: Standard interfaces
Many stdlib APIs are built on small interfaces:
- `io.Reader/Writer`
- `error`
- `fmt.Stringer`

This is why “accept interfaces, return structs” is idiomatic.

**Mini‑exercises**
1. Create `Notifier` and implementations.
2. Write `ReadAllAndCount(r io.Reader)`.

---

## Chapter 2.4: Encapsulation & Package Design

### Section 2.4.1: Export rules
Uppercase → exported. Lowercase → package-private.

### Section 2.4.2: Package boundaries
Packages should represent **cohesive concepts**, not layers. Keep dependencies one-directional to avoid cycles.

### Section 2.4.3: Avoiding import cycles
Break cycles by:
- moving shared types to smaller packages
- using interfaces to invert dependency
- rethinking ownership

**Mini‑exercise**
- Refactor a cyclic design into acyclic packages.

---

## Chapter 2.5: Errors (Deep)

### Section 2.5.1: Sentinel errors
Sentinel errors are shared package-level variables for common cases.

```go
var ErrNotFound = errors.New("not found")
```

### Section 2.5.2: Custom error types
Custom errors can carry metadata.

```go
type ValidationError struct {
	Field string
	Msg string
}
func (e ValidationError) Error() string { return e.Field + ": " + e.Msg }
```

### Section 2.5.3: Wrapping and inspection
Wrapping preserves root cause while adding context.

```go
return fmt.Errorf("load user: %w", err)
```

Inspection:
```go
if errors.Is(err, ErrNotFound) { ... }

var ve ValidationError
if errors.As(err, &ve) { ... }
```

### Section 2.5.4: Panic vs error
- **error** for expected failures (validation, I/O, timeouts)
- **panic** for impossible programmer states

**Mini‑exercises**
1. Build a `JoinErrors` helper using `errors.Join`.
2. Write a handler that maps errors to HTTP status codes.

---

# Part 3: Generics, Collections, and Functional-ish Patterns

## Chapter 3.1: Generics (Go 1.18+)

### Section 3.1.1: Why generics?
Before generics, Go used:
- `interface{}` + type assertions
- reflection
- code generation

Generics let you write reusable code **without losing type safety**.

### Section 3.1.2: Generic functions
```go
func Map[T any, R any](in []T, f func(T) R) []R {
	out := make([]R, len(in))
	for i, v := range in { out[i] = f(v) }
	return out
}
```

### Section 3.1.3: Constraints
Constraints define what operations are legal on `T`.

```go
type Number interface{ ~int | ~int64 | ~float64 }

func Sum[T Number](xs []T) T {
	var total T
	for _, x := range xs { total += x }
	return total
}
```

- `any` means no constraints.
- `comparable` means values support `==`/`!=`.

### Section 3.1.4: Generic types
```go
type Stack[T any] struct{ items []T }

func (s *Stack[T]) Push(v T) { s.items = append(s.items, v) }
func (s *Stack[T]) Pop() (T, bool) { ... }
```

### Section 3.1.5: When NOT to use generics
Generics can hurt readability if:
- constraints are complex
- abstraction doesn’t pay off
- interface already expresses behavior properly

**Mini‑exercises**
1. Implement `Filter[T any]`.
2. Create `Set[T comparable]`.

---

## Chapter 3.2: Maps

### Section 3.2.1: Map theory
Maps are hash tables. Reads are safe for concurrent use; **writes are not** (need synchronization).

### Section 3.2.2: Initialization
```go
m := map[string]int{"a":1}
m2 := make(map[string]int, 10)
```

### Section 3.2.3: Existence checks
```go
v, ok := m["x"]
```

### Section 3.2.4: Iteration order
Go randomizes map iteration to discourage order reliance.

### Section 3.2.5: Value vs pointer values
Prefer value structs unless you need shared mutability.

**Mini‑exercise**
- Word frequency counter.

---

## Chapter 3.3: Advanced Slice Patterns

### Section 3.3.1: Reusing backing arrays
In performance paths, re-slice to zero length to reuse allocations:

```go
out := xs[:0]
for _, x := range xs {
	if pred(x) { out = append(out, x) }
}
```

### Section 3.3.2: Sorting and helpers
```go
sort.Slice(users, func(i, j int) bool {
	return users[i].ID < users[j].ID
})
```
Go 1.21+ adds generic helpers in `slices` and `maps`.

```go
slices.Sort(xs)
ys := slices.Delete(xs, 2, 4)
keys := maps.Keys(m)
```

**Mini‑exercises**
1. Implement `TopN`.
2. Use `slices.Compact` to dedupe adjacent duplicates.

---

# Part 4: Concurrency & Go Runtime

## Chapter 4.1: Goroutines

### Section 4.1.1: Theory
A goroutine is a lightweight, runtime-managed thread:
- small initial stack (grows/shrinks)
- cheap creation
- scheduled by Go runtime, not OS directly

```go
go doWork()
```

### Section 4.1.2: Waiting
Use `sync.WaitGroup` to wait for goroutines.

**Mini‑exercise**
- Start N goroutines and wait for all.

---

## Chapter 4.2: Channels

### Section 4.2.1: Theory
Channels provide:
1. **communication**
2. **synchronization**
3. **ownership transfer**

Unbuffered channels synchronize sender and receiver; buffered allow bursts.

### Section 4.2.2: Basic usage
```go
ch := make(chan int)
go func(){ ch <- 42 }()
v := <-ch
```

### Section 4.2.3: select
`select` waits on multiple channel ops.

### Section 4.2.4: Close and range
Close is a broadcast that no more values will be sent.

### Section 4.2.5: Fan-out/fan-in patterns
Worker pool, pipelines, etc.

**Mini‑exercise**
- Pipeline: gen → square → sum.

---

## Chapter 4.3: Context

### Section 4.3.1: Theory
`context.Context` carries:
- cancellation signal
- deadline/timeout
- request-scoped values

It is the standard way to bound work in servers.

### Section 4.3.2: Usage
Always pass `ctx` first.

**Mini‑exercise**
- Cancel a worker pool after 1 second.

---

## Chapter 4.4: `sync` Package Essentials

### Section 4.4.1: Theory + tools
- `WaitGroup`: join goroutines
- `Mutex/RWMutex`: mutual exclusion
- `Once`: one-time init
- `Cond`: coordination
- `Pool`: reuse allocations
- `atomic`: lock-free primitives (typed since 1.19)

### Section 4.4.2: Race detector
Data races are undefined; use `-race`.

**Mini‑exercise**
- Fix a race in shared map writes.

---

## Chapter 4.5: Memory Model Basics

### Section 4.5.1: Theory
You need synchronization to guarantee visibility between goroutines.
- channel send happens-before receive
- mutex unlock happens-before next lock

### Section 4.5.2: Practical meaning
If two goroutines access same variable and at least one writes, you need channels, mutexes, or atomics.

**Mini‑exercise**
- Explain why `close(ch)` makes values visible.

---

# Part 5: I/O, Networking, and Standard Library Power

## Chapter 5.1: Files & Directories

### Section 5.1.1: Reader/Writer philosophy
Go models I/O as streams. Many APIs take `io.Reader` or `io.Writer`, which enables easy composition, testing, and memory efficiency.

### Section 5.1.2: Small file read/write
```go
data, _ := os.ReadFile("a.txt")
_ = os.WriteFile("b.txt", data, 0644)
```

### Section 5.1.3: Streaming big files
Use `io.Copy` or buffered readers.

**Mini‑exercise**
- Implement file copy with progress.

---

## Chapter 5.2: JSON & Encoding

### Section 5.2.1: Theory
`encoding/json` uses reflection and tags to map Go structs to JSON. Fields must be exported.

### Section 5.2.2: Custom marshaling
Implement `MarshalJSON/UnmarshalJSON` for special formats.

**Mini‑exercise**
- Custom date type.

---

## Chapter 5.3: HTTP in Go

### Section 5.3.1: Theory
`net/http` is built around `http.Handler`:
```go
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}
```
Middleware is just handler wrapping.

### Section 5.3.2: Client and server
Examples shown earlier; add timeouts and context to avoid leaks.

### Section 5.3.3: Graceful shutdown
Always support SIGTERM and stop accepting new connections.

**Mini‑exercise**
- Add middleware for logging + recovery.

---

## Chapter 5.4: Testing

### Section 5.4.1: Theory
Go tests are in `_test.go` files. The testing tool compiles test binary and runs it.

### Section 5.4.2: Table tests, subtests, benchmarks, fuzzing
(see examples earlier)

**Mini‑exercise**
- Write benchmark for two JSON strategies.

---

# Part 6: Modern Go Evolution (Go 1.18 → Go 1.23+)

## Chapter 6.1: Release Matrix Overview
| Release | What changed | Why it matters |
|---|---|---|
| 1.18 | Generics, fuzzing, workspaces | type-safe reuse; safer code; multi-module dev |
| 1.19 | Memory model update; typed atomics | clearer concurrency; less atomic misuse |
| 1.20 | errors.Join; unsafe helpers; perf | better multi-error handling; low-level interop |
| 1.21 | slices/maps/cmp; min/max/clear; toolchain mgmt | first-class collection helpers; smoother upgrades |
| 1.22 | range loop var fix; range over ints; net/http routing | kills classic closure bug; nicer stdlib routing |
| 1.23+ | range over iterators; iter/unique/structs pkgs; timer/GC/tooling improvements | lazy sequences; safer timers; better runtime |

## Chapter 6.2: Go 1.18
- **Generics:** type parameters + constraints.
- **Fuzzing:** native fuzz tests in `go test`.
- **Workspaces:** `go.work` for multi-module.

Why it matters: reduces boilerplate, improves test robustness, and speeds local service dev.

Example (generics) in Part 3.

Gotchas: avoid over-generalizing.

## Chapter 6.3: Go 1.19
- Memory model clarified.
- Typed atomics introduced: `atomic.Int64`, etc.
- Runtime/Scheduler improvements.

Why: easier reasoning and fewer concurrency bugs.

Example:
```go
var v atomic.Int64
v.Add(1)
```

## Chapter 6.4: Go 1.20
- `errors.Join` for multi-error.
- new unsafe slice/string helpers.
- perf improvements.

Example:
```go
return errors.Join(err1, err2)
```

Gotchas: don't use unsafe unless required.

## Chapter 6.5: Go 1.21
- `slices`, `maps`, `cmp`.
- built-ins `min`, `max`, `clear`.
- toolchain selection.

Example:
```go
slices.Sort(xs)
clear(m)
```

## Chapter 6.6: Go 1.22
- loop variable capture fixed.
- `for i := range 10` over integers.
- stdlib routing patterns.

Example:
```go
for _, v := range xs {
	go func() { fmt.Println(v) }()
}
```

## Chapter 6.7: Go 1.23+
- `range` over iterator functions (lazy iteration).
- new packages: `iter`, `unique`, `structs`.
- timers made safer; runtime perf.

Iterator example:
```go
func CountUpTo(n int) func(func(int) bool) {
	return func(yield func(int) bool) {
		for i := 0; i < n; i++ {
			if !yield(i) { return }
		}
	}
}
for i := range CountUpTo(5) { fmt.Println(i) }
```

Experimental: generic type aliases behind `GOEXPERIMENT=aliastypeparams`.

---

# Part 7: Tooling, Performance, and Deployment

## Chapter 7.1: Go Tooling
- `go fmt`: formatting
- `go vet`: suspicious patterns
- `go test`: tests/bench/fuzz/race
- `go list`: package metadata
- `go doc`: docs

Practice: wire these into CI.

## Chapter 7.2: Modules Deep Dive
- `go mod tidy`
- `replace` for local dev
- `vendor` for hermetic builds
- `go mod tidy -diff` (1.23) for previewing changes

## Chapter 7.3: Profiling & Tracing
- `pprof` CPU/memory for hotspots/allocations
- `go tool trace` for scheduler/GC investigation

Workflow: benchmark → profile → optimize → reprofile.

## Chapter 7.4: Build & Release
- cross compile with `GOOS/GOARCH`
- static binaries with `CGO_ENABLED=0`
- build tags for variants
- `//go:embed` to ship assets inside binary

---

# Part 8: Ecosystem & Best Practices

## Chapter 8.1: Project Structure
Canonical layout:
```
cmd/
internal/
pkg/
```
- keep internal private
- only put truly reusable libraries in pkg

## Chapter 8.2: Code Style
- small interfaces
- explicit errors
- avoid “futures/promises” patterns; use goroutines/channels
- no “util” mega-packages
- follow gofmt always

## Chapter 8.3: Common Architectural Patterns in Go
- pragmatic clean/hexagonal style
- DI via constructors
- worker pools for controlled concurrency
- retry/backoff for unreliable networks

Retry example:
```go
func Retry(ctx context.Context, attempts int, fn func() error) error {
	var err error
	backoff := 50 * time.Millisecond
	for i := 0; i < attempts; i++ {
		if err = fn(); err == nil { return nil }
		select {
		case <-ctx.Done():
			return ctx.Err()
		case <-time.After(backoff):
			backoff *= 2
		}
	}
	return fmt.Errorf("after %d attempts: %w", attempts, err)
}
```

---

## Final Practice Projects
1. **Concurrent Log Processor**: worker pool + context + pprof.
2. **URL Health Checker**: retries, deadlines, graceful shutdown.
3. **Tiny Store API**: CRUD + Postgres + table tests/benchmarks.
4. **Generic TTL Cache**: `Cache[K comparable, V any]`.

---

**You’re done.** You now have the language, runtime, and idioms needed to build serious Go backends and tooling.

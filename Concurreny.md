# CONCURRENCY VS PARALLELISM
1. **CONCURRECY**

Concurrency means dealing with multiple tasks at the same time, but not necessarily executing them simultaneously.

It’s about structure how you design your system to handle multiple tasks that may overlap in time.


```“Starting multiple things, but not necessarily finishing them at the same moment.”```

Example
Imagine you’re a single person (single-core CPU) cooking two dishes:

- You boil water for pasta.
- While water boils, you chop vegetables.
You’re not doing both exactly at the same time you switch between tasks quickly.

That’s concurrency — you manage multiple tasks by interleaving them.

2. **PARALELLISM**
Parallelism means executing multiple tasks literally at the same time. This requires multiple processors or cores.

```“Doing multiple things exactly at the same time.”```

Example
Now imagine you and your friend are cooking together:

- You boil water.
- Your friend chops vegetables.

Both actions truly happen simultaneously.
That’s parallelism.

# CONCURRENCY IN GO
Concurrency in go is achieved using **GOROUTINES**.

1. What is goroutine.

A goroutine is a lightweight thread managed by the Go runtime.
It allows you to run a function concurrently with other functions.

- Think of it like starting a mini-program inside your program that runs independently.

-  Goroutines are very cheap compared to OS threads. You can have thousands of them running at the same time.

`goroutines` can be in one of the three states
1. blocked
2. runnable
3. running

## GoScheduler

lightweight, built-in scheduler inside the Go runtime that decides which goroutine runs on which OS thread and when.

*Think of it as Go’s replacement for the event loop in Node.js or the OS scheduler for threads — but specifically designed to handle millions of goroutines efficiently.*

Go’s scheduler is often explained using three components:

| Term | Meaning               | Role                                 |
|------|-------------------------|----------------------------------------|
| G    | Goroutine               | Lightweight task to run (your functions) |
| M    | Machine (OS thread)     | Where goroutines actually execute     |
| P    | Processor (logical context) | Schedules goroutines onto threads     |

Analogy:

- G = people waiting to work

- P = managers giving tasks

- M = workers (actual machines doing the work)

**Inorder to create a goroutine you have to use `go` followed by function**

Example
```go
package main
import "fmt"
func main(){
    fmt.Println("START")
    go fun(){
        fmt.Println("INSIDE GOROUTINE")
    }()
    fmt.Println("END")
}
```
Output:
```sh
START 
END
```
Here why didn't `INSIDE GOROUTINE` got printed on to console?.

This is what happens step by step.

1. `fmt.Println("START")` -> prints START.
2. `go func() { ... }()` -> → starts a new goroutine, which is like a mini-thread. It runs at the same time as the main function.
3. `fmt.Println("END")` -> prints END immediately, without waiting for the goroutine.
4. The main function finishes and exits.
5. When the main function exits, the program ends even if goroutines are still running.

Here the main function is not waiting for the goroutine to get finished. So how do we wait for the go routine to get finished?.

**To Make the program wait for goroutine to finish we can use something called `WaitGroup`.**

A `WaitGroup` let's the main function wait untill all goroutines finish their work. 
```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var wg sync.WaitGroup  // create a WaitGroup

    fmt.Println("START")

    wg.Add(1) // tell WaitGroup we're adding 1 goroutine
    go func() {
        defer wg.Done() // mark this goroutine as done when it finishes
        fmt.Println("INSIDE GOROUTINE")
    }()

    wg.Wait() // wait for all goroutines to finish
    fmt.Println("END")
}
```
Step by Step:
1. `wg.Add(1)` → we tell the WaitGroup: "Hey, I have 1 goroutine to wait for."
2. `go func() { ... }()` → launch the goroutine.
3. `defer wg.Done()` → when the goroutine finishes, it tells the WaitGroup: "I’m done!".
4. `wg.Wait()` → main function pauses here until all goroutines are done.
5. After that, `fmt.Println("END")` runs.

Output:
```sh
START
INSIDE GOROUTINE
END
```
### RaceCondition.
1. Race condition occurs when two or more go routines try to access same variables at the same time. 
2. And at least one of them is modifying that data.
3. And the final result depends on timing, which can change every time you run the program.

Example:
```go
var count = 0
for i := 0; i < 1000; i++ {
    go func() {
        count++
    }()
}
time.Sleep(time.Millisecond * 100)
fmt.Println(count)
```
Here in this case we might think that we will get `1000` as output.
That can't be guaranteed. 

**Let's see what's happening here.**
- Launched 1000 goroutines incrementing count.
- But they all access and modify count at the same time.
- Sometimes two goroutines read the same value before any one writes it back.
- So the updates overlap and some increments get lost.

**That’s why the final output is often less than 1000.**

### How do we fix Race condition.
Use Mutex(Mutual Exclusion Lock).

A `sync.Mutex` allows only one goroutine to access a piece of code at a time.
```go
var count = 0
var mu sync.RWMutex

func main() {
    for i := 0; i < 1000; i++ {
        go func() {
            mu.Lock()          // lock before accessing shared variable
            count++
            mu.Unlock()        // unlock after done
        }()
    }

    time.Sleep(time.Second)
    fmt.Println("Final count:", count)
}
```
Now the ouput will always be:
```sh
Final count: 1000
```
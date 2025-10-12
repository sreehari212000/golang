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
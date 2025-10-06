# INTRODUCTION TO GO

## Before you start writing code in go the first thing you have to do is
```
go mod init
```
this is almost similar to npm init

## Basic syntax
```go
package main
import ("fmt")
func main(){
    fmt.println("Hello World!")
}
```
## Data Types
1. Integer - int, int32, int64, int8, int16...
2. String - string
3. Boolean - bool
4. Float - float32, float64


## Arrays 
To intialize an array
```go
var arr []int = []int{1,2,3}
var arr = []int{1,2,3}
```

## Taking input from users
There are multiple ways to take input from users
1. If you know what type of input will be provided by user. 

using Scanln
```go
package main
import "fmt"

func main() {
    var name string
    fmt.Print("Enter your name: ")
    fmt.Scanln(&name) // reads input until newline
    fmt.Println("Hello,", name)
}
```
using scanf
```go
package main
import "fmt"

func main() {
    var a int
    var b float64

    fmt.Print("Enter an integer and a float: ")
    fmt.Scanf("%d %f", &a, &b)

    fmt.Println("Integer:", a, "Float:", b)
}

```
2. Using bufio.NewReader
```go
package main
import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    reader := bufio.NewReader(os.Stdin)
    fmt.Print("Enter your full name: ")
    name, _ := reader.ReadString('\n') // reads until Enter is pressed
    fmt.Println("Your name is:", name)
}

```
## Structs
In golang there is no inheritence, ie no super or parent 
To create a struct 
```go
type User struct {
	Name      string
	email     string
	age       int
	isPremium bool
}
```
### Creating Methods 
A copy of the object is passed to the function when passing a parameter to the method
```go
type User struct {
	Name      string
	email     string
	age       int
	isPremium bool
}
func (u User) getName(){
	fmt.Println("The name of user is", u.Name)
}
func main(){
    u := User{Name: "Sreehari", email: "sreehari@email.com", age: 24}
}
```
## Defer
The defer statement is used to delay the execution of a function until the surrounding function returns.
```go
package main

import "fmt"

func main() {
    fmt.Println("Start")

    defer fmt.Println("Deferred message") // executed last
    fmt.Println("Middle")

    fmt.Println("End")
}

```
output
```
Start
Middle
End
Deferred Message
```
Defer functions runs in **LIFO** order
```go
package main

import "fmt"

func main() {
    defer fmt.Println("First")
    defer fmt.Println("Second")
    defer fmt.Println("Third")
}

```
Output
```
Third
Second
First

```
Defer function captures values immedietly
```go
package main

import "fmt"

func main() {
    x := 10
    defer fmt.Println("Value of x:", x) // captures x = 10
    x = 20
}

```
Output
```
Value of x: 10

```

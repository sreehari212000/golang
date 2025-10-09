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
## *panic* Keyword
```panic``` is used in Go to stop normal execution immediately when something goes terribly wrong (like a runtime error or an unrecoverable situation).

Think of it as throwing an error in Node.js.
```go
package main

import "fmt"

func riskyDivision(a, b int) int {
    if b == 0 {
        panic("division by zero")
    }
    return a / b
}

func main() {
    fmt.Println("Start")
    fmt.Println(riskyDivision(10, 0))
    fmt.Println("End") // never reached
}

```
```
Start
panic: division by zero
```

## Working with files
Like other programming languages you can only work with text files. 

We use **os** module to work with files

1. Creating a file and writing to it.
```go
file, err := os.Create("./sample.txt")
if err != nil {
    panic(err)
}
length, err := io.WriteString(file, "Hello, my name is sreehari")
defer file.Close()
```
2. Reading files
```go
fmt.Println("Reading from a file")
bytes, err := os.ReadFile("./sample.txt")
if err != nil {
    panic(err)
}
fmt.Println("The content of the file is ", string(bytes))

```

## Handling Web Requests.
We make use of net/http and io to handle web requests.
```go
res, err := http.Get("https://jsonplaceholder.typicode.com/posts")
if err != nil {
    panic(err)
}
defer res.Body.Close()
databytes, err := io.ReadAll(res.Body)
if err != nil {
    panic(err)
}
fmt.Println(string(databytes))
```
## Working with URL's
We use ```url``` module for this.
```go
const myUrl string = "https://www.quickzy.ai/api/v1/posts?page=1&sort=desc"
// convert a string into URL
ulrString, err := url.Parse(urlString)
// to get query params from a URL
qparams := urlString.Query()
```
To contstruct a url from chunks
```go
partsOfURL := &url.URL{
    Scheme: "https",
    Host: "isai.dev",
    path: "/learn"
}
ur := partsOfURL.string()
```
There are any methods in URL. **CHECK IT OUT**.

## Working with JSON 


```go
package main

import (
	"encoding/json"
	"fmt"
)
type Course struct {
	Name     string   //`json:"name"`
	Price    int      //`json:"price"`
	Password string   //`json:"password"`
	Tags     []string //`json:"tags"`
}


func EncodeJSON() {
	courses := []Course{
		{"React", 300, "react@password", []string{"web", "frontend"}},
		{"Node.js", 300, "node@password", []string{"web", "backend"}},
		{"Docker", 300, "docker@password", nil},
	}
	finalJSON, err := json.Marshal(courses)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s\n", finalJSON)
}

func main() {
	EncodeJSON()
}

``` 
When you create a struct in Go, like this:
```go
type Course struct {
	name     string
	price    int
	password string
	tags     []string
}
```
In Go, any identifier that starts with an uppercase letter is exported and accessible from other packages, while those that start with a lowercase letter are unexported and only accessible within the same package.

The encoding/json package can only see public (exported) fields — those that start with a capital letter.
So when you try to convert your struct to JSON, it can’t see the lowercase fields, and the result becomes:
```
[{}, {}, {}]
```
Also to avoid a certain field to not show when consuming API
```go
type Course struct {
	name     string
	price    int 
	password string `json:"-"`
	tags     []string `json:"tags,omitempty"` //if the value is null then don't show the field.
}
// this ensures that password field does not come in the result
```
#### In short:

```lowercase → private → not visible to JSON```

```capitalized → public → visible to JSON```

## Consuming JSON data.
```go
func decodeJSON() {
	jsonData := []byte(`
	{
		"name": "React.js",
		"price": 200,
		"tags": ["web", "frontend", "js"]
	}
	`)
	js := json.Valid(jsonData)
	var lc Course //the struct created before
	if js {
		fmt.Println("VALID JSON")
		json.Unmarshal(jsonData, &lc) 
		fmt.Println(lc)
	} else {
		fmt.Println("INVALID JSON")
	}
} 
```
```json.Marshal()``` is used to conver Go variable into JSON data.


```json.Unmarshal()``` is used to convert JSON data back into a Go variable (like a struct, map, or slice).

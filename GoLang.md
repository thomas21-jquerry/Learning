Golang (or Go) is an open-source programming language developed by Google. It is known for its simplicity, efficiency, and strong support for concurrent programming.  Goroutines are lightweight, and Go's runtime manages their execution, making it easier to write highly concurrent applications.
Channels are used for communication between goroutines, which simplifies synchronization.

Go is a statically-typed language, which helps catch errors at compile time and provides better performance compared to dynamically-typed languages. It is strongly typed

Go has an efficient garbage collector, making memory management easier and less error-prone compared to languages that require manual memory management

Go does not have traditional object-oriented programming features like classes or inheritance. Instead, it uses composition to achieve similar functionality through structs and interfaces.  

var myVar string 

package main		this is the start of the execution	
import “fmt” 

func main() {
	fat.Println("Hello World!")
}

To compile run 
go build cmd/folder/<nameOfFile.go>

./<nameOfFile>

Or 

go run cmd/folder/<nameOfFile.go> (runs in a single go)

## Variables

var myVar string 
integers (int8, int16, int32, int64) it overflows if large value comes normal int without suffix is int16 and store 32767 is the highest value
cannot do arithmetic between each other or type cast it manually

float (float32, float64)

string 
len(stringVar) gives the length of the byte not the actual length so some special char take 2 spaces. 
to avoid this import "unicode/utf8" and use utf8.RuneCountInString(stringVar)

rune
this represents a char

bool
true / false

if you dont specify the val of a var then its default is either 0, false or ''

another way of declaration
myVar := "text"
a,b := 1, 2

const 
cannot declare needs value and cannot be changed

## Functions
func 


package main		this is the start of the execution	
import (
    "errors"
    “fmt”
) 
func printMe(var1 string) string {
    // do something
    return var1
}

func div(var1 int, var2 int) (int, int) {
    var err error  // special type error default val is nil
    if denominator == 0{
        err = errors.New("cannot divide by zero")
        return 0,0,err
    }
    var result int = var1/ var2
    var rem = var1 % var2
    return result, rem
}


# conditions

if 1==1 && 2!=1 {

} else if 1!=1 || 2!=1{

} else {

}

switch reminder {
    case 0:
        // do something
    case 1: 
        // do something
    default: 
        // default statement
}

# Arrays
Fixed Length, same type, indexable, contigouus in memory

var intArr [3]int32
var intArr[3]int32 = [3]int32{1,2,3}

access the ith element intArr[i]
access 0->i elements intArr[0:i+1]

Slices
these are dynamic array
var intSlice []int32
we can use the intSlice.append()

var intSlice []int32 = make(int[],3,8)      // 3 is the initial length and 8 is the capacity

# Map
key value pair

var myMap map[string]uint8 = make(map[string]uint8)
var myMap = map[string]uint8{"adam":23, "sarah" 45}
car val, ok = myMap["adam"]

to delete a entry use delete
delete(myMap,"adam")

# Loops
for name, age := range myMap{
    //do something
}

for i, v := rangeintArr{

}

no while loop in go instead u can use 
for i<10{
    // do something
    i=i+1
}

for i:=0;i<10;i++{

}


# structs
type gasEngine struct {
    mpg uint8
    gallons unit8
}

func (e gasEngine) milesLeft() unit8 {  // this how you define functions of struct . yes outside the struct 
    return e.gallons * e.mpg
}

func main(){
    var myEngine gasEngine = gasEngine{24,14}
    myEngine.milesLeft()
}

you can also define anonymous struct
var engine2 = struct {
    mpg uint8
    gallons uint8
}{25, 15}

# interfaces
type engine interface {
    milesLeft() uint8
}

# Pointers

var p *int32 // allocate 32bits or 64 bits based on architecture
var p *int32 = new(int32) // address of the starting address of p is stored
*p = 10
fmt.Printf("p = %v", *p)


# Go Routines
Go goroutines are lightweight threads managed by the Go runtime. They allow you to run functions concurrently without the need for complex thread management, making concurrent programming much simpler and more efficient.
it uses the go keyword
go somefunction()

example 
package main

import (
	"fmt"
	"time"
)

func sayHello() {
	fmt.Println("Hello from Goroutine!")
}

func main() {
	// Start a new goroutine
	go sayHello()

	// The main function will wait a bit before exiting
	// so we can see the goroutine output
	time.Sleep(time.Second)
}


## Wait group
To wait for the go routines to be done before exiting

example
package main

import (
	"fmt"
	"sync"
	"time"
)

func doWork(id int, wg *sync.WaitGroup) {
	defer wg.Done() // Decrement the counter when the goroutine finishes
	fmt.Printf("Goroutine %d is working\n", id)
	time.Sleep(time.Second)
	fmt.Printf("Goroutine %d has finished\n", id)
}

func main() {
	var wg sync.WaitGroup

	// Start multiple goroutines
	for i := 1; i <= 3; i++ {
		wg.Add(1) // Increment the counter for each goroutine
		go doWork(i, &wg)
	}

	// Wait for all goroutines to finish
	wg.Wait()
	fmt.Println("All goroutines have finished")
}

defer wg.Done() inside the doWork function decrements the counter when the goroutine completes.
wg.Add(1) increments the counter to indicate that a new goroutine is being launched.
wg.Wait() blocks the main function until all goroutines finish their work.

# Channel
Channels in Go are used to allow goroutines to communicate with each other. A channel is a typed conduit through which you can send and receive values between goroutines.
example
package main

import "fmt"

func generateNumbers(ch chan int) {
	for i := 1; i <= 5; i++ {
		ch <- i // Send value to the channel
	}
	close(ch) // Close the channel after sending all values
}

func main() {
	ch := make(chan int) // Create a new channel of type int

	// Start a goroutine to generate numbers
	go generateNumbers(ch)

	// Receive values from the channel
	for num := range ch {
		fmt.Println(num)
	}

	fmt.Println("All numbers received")
}




package main

import (
	"fmt"
	"sync"
)

func calculateSquare(id int, num int, ch chan<- int, wg *sync.WaitGroup) {
	defer wg.Done() // Decrement the counter when the goroutine finishes
	square := num * num
	ch <- square // Send the result to the channel
}

func main() {
	var wg sync.WaitGroup
	ch := make(chan int, 5) // Buffered channel

	numbers := []int{1, 2, 3, 4, 5}

	// Start goroutines to calculate squares
	for _, num := range numbers {
		wg.Add(1)
		go calculateSquare(num, num, ch, &wg)
	}

	// Wait for all goroutines to finish
	wg.Wait()
	close(ch) // Close the channel after all goroutines are done

	// Receive and print results from the channel
	for square := range ch {
		fmt.Println(square)
	}
}

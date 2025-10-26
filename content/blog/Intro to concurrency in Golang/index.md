+++
title = "Introduction to concurrency in Golang"
date = 2025-10-26
authors = ["Hamza Mateen"]

[taxonomies]
tags = ["showcase", "shortcodes"]
+++

### Introduction
**Rob Pike**, one of the creators of the Go programming language, is often attributed with the following quote:

> Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.

Simplest real life example to this could arguably be multi-tasking. When we multi-task, we are basically switching from one **task** to another in, hopefully, an efficient manner. That's how we get *multiple* tasks done in lesser time. That's concurrency for you!

Now imagine you have got a few friends to help **you** through those tasks. Each of you assign yourself to a specific task and then begin the grind. Some time later, every **friend** including *you* finishes their respective task. This is an example of parallelism.

There exist programming analogues of the few terms that I have emphasized above, these are:

**Task**: Any job that is supposed to be performed by a computer program.
**You** : Signifies the *main thread* of the program which is performing the said task.
**Friend** : This is the *helper thread* that helps the main thread in getting the task done quickly.

With that out of the way, we can now talk about how Go deals with concurrency.

# Go Concurrency Model

Go introduced a few **constructs** to make it easy for the developers to create concurrently executable programs, namely:

1. Goroutines (co-routines in other languages)
2. Channels

## Goroutines

Go uses functions to facilitate the use of goroutines. 
> A *goroutine* is a function that runs concurrently to other functions.

It is functionally synonymous to an operating system thread because it is an independent unit of execution but there exist several differences, which make goroutines a better choice in terms of concurrency. 

For instance, *goroutines*:

* are lightweight
* have minimal memory-footprint.
* are easy to create and manage (via channels).
* are scheduled in user-space by the Go scheduler instead of kernel-space by OS kernel.
* have low context-switching cost.

In Go, if *function call* is preceded by the keyword **go**, it becomes a goroutine. In code, it looks like this:

```Go
package main

func square(num int) {
    return num * num
}

func main() {
    go square(2); 
}
```

In Go, every function *qualifies* to be a goroutine, which implies that a program's *main* function can also be a goroutine.

In above code snippet, there are two goroutines; *main* and the *greetGuest*, former is made into a goroutine by *Go runtime* whereas *greetGuest* becomes a goroutine as its invocation follows the **go** keyword.
In this way, the *greetGuest* function runs concurrently with the *main* function, and executes its instructions independently.

The *main* function after creating the goroutine, immediately ends because there are no more instructions to run. It does not wait for the *greetGuest* goroutine to finish its job. We can change this behaviour, and make it wait but we will look into this later in the article.

As you might have noticed, we didn't store the squared value returned by the square goroutine. This is because we cannot!  Goroutines are not ordinary functions; they are mini-programs that perform specialized tasks which they are created for. 

But - what if we absolutely need a goroutine to return a value before terminating? For example, let's say we want to compute the arithmetic sum of an array by decomposing it into subarrays and then process each one using a goroutine? Logically, those goroutines would've to send the computed values back to the *main* goroutine; this is where **channels** come into play. Channels are the **construct** introduced by Go to manage and synchronize goroutines effectively.


## Channels
> Channels provide a medium for goroutines to communicate with each other. 
 
They can be used to synchronize the execution and state across goroutines. A channel can be created with its name followed by the keyword **chan <type>** where *type* is any built-in data-type.
Channels are shared-memory blocks under the hood. When a channel is created, it is basically a pointer to that memory block which we can share around with goroutines for mutual access. 

```Go
var ch chan int
```

Here, ```ch``` is a ```nil``` channel of type ```int``` which can neither store nor transmit any value because it is not initialized. We can pass it to the built-in ```make``` function, which allocates and initializes an object of various types including channel.

```Go
ch := make(chan int)
```

This is now a proper channel which can help share data among goroutines.

### Communication Types 
Channels facilitate communication between goroutines but communication has several types, such as:

    1. Simplex communication
    2. Half-duplex communication
    3. Full-duplex communication

**Simplex** communication is a unidirectional or a one-way communication scheme where only one side can send data. The receiving side cannot send any information back to the transmitting side. **Publisher-Subscriber** design pattern is based on simplex communication because data flows only from *publisher* to the *subscriber* and not the other way around.

**Half-duplex** communication occurs when both parties are capable of receiving and transmitting data but only one side can send data at a time. It is sort of a **synchronous** form of communication in the sense that it requires co-ordination from both sides because the data must be consumed by a side before the channel is ready to receive further data.

**Full-duplex** is a **synchronous**, two-way communication system where both sides can send and receive data simultaneously. Examples include chat messaging systems, and real-time video-conferencing.

Go enables these communication mechanisms through **Buffered** and **Unbuffered** channels in addition to multi-channel communication.

### Buffered vs. Unbuffered Channels

An **unbuffered** channel can be created using the ```make``` function without specifying a buffer size. The 
```make``` function, by default, creates a channel of unit capacity, which means that it can hold only one value at a time. Both sides can send and receive data from such a channel but only when the channel is empty. If it's not then the value must first be consumed by either the sender or receiver goroutine. Unbuffered channels help us establish partially **synchronous** communication system between goroutines.

To create a **buffered** channel to establish **asynchronous** communication between goroutines, a *buffer size* can be passed to the ```make``` function along with the channel type. For example:

```Go
ch := make(chan int, 2)
```

This creates a buffered channel with capacity of holding 2 integer values simulataneously, so the sender, after sending a value to the channel, doesn't have to wait for the receiver to read it, before sending another one. 

This communication mode is asynchronous, however, it's not a **Full-duplex** communication system. That could only be achieved if we opened two separate channels instead of increasing the channel capacity. Full-duplex communication requires both parties to be able to send and receive messages at the **same** time, which a single channel cannot allow because concurrent access without blocking can introduce data races.

Let's work through an example to understand the usage and power of **Channels**

## A Nice Concurrency Problem
> Calculate the sum of subarrays concurrently!

First, create a function that receives a channel and an array to sum. 
    
We initalize the ```sum``` variable, then use a ```for``` loop to compute the sum of array. Lastly, the computed value is written into the channel with special syntax that is ```<-``` (an arrow) **after** the channel name.


```Go
func sumSubarray(ch chan int, subarr []int) {
	// initialize the sum variable
	sum := 0

	// compute the sum
	for i := 0; i < len(subarr); i++ {
		sum += subarr[i]
	}

	// write the sum into channel
	ch <- sum
}
```

Next, we initialize a channel and a supposedly large integer array, inside the main function. We also calculate the mid-point of array to split it in two equal parts in order to distribute it between a couple of goroutines.


```Go
func main() {
	// communication channel
	ch := make(chan int)

	// supposedly large array of integers
	arr := [19]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19}

	// calculate the mid-point for splitting
	mid := int(len(arr) / 2)
}
```

Now, a ```totalSum``` variable is initialized to ```0``` to hold the combined sum from different goroutines. Then, a couple of goroutines are created to sum the array segments. We pass the channel ```ch``` created above, and the intended segments to each goroutine. 
          
When the first goroutine is created, the main function doesn't wait for the result and continues its execution. It then creates another goroutine to calculate the sum of 2nd half. Array splitting is done through ```slicing``` which follows the syntax: ```arr[start:end]```, where the starting index is inclusive while the ending index is exclusive. When the 'end' is omitted, it means to slice through *till the end of array*.

```Go
func main() {
    // ...
    // ...
 
	totalSum := 0

	// create goroutines to
	go sumSubarray(ch, arr[0:mid])
	go sumSubarray(ch, arr[mid:])
}
```

Finally, we are ready to receive the partial sums from those goroutines.

```Go
func main() {
    // ..
    // ..

	// collect the results
	totalSum += <-ch
	totalSum += <-ch

	// print the results
	fmt.Println("Total sum: ", totalSum)
}
```

To receive from a channel, an arrow **before** its name is placed. We receive a value from the channel, and add it to the ```totalSum``` variable. This is a **blocking** operation because we are reading from an empty channel. Execution of the main function would be blocked until one of the goroutines produces a result and sends it to the channel. When it happens, the main function immediately reads it, and adds it to the variable, making the channel empty again. The next instruction would also be a blocking one. We wait until the final goroutine returns a value into the channel which gets added to the total sum.

Finally, the ```totalSum``` is printed to the console.

Here's all the code for reference:
```Go
package main

import "fmt"

func sumSubarray(ch chan int, subarr []int) {
	// initialize the sum variable
	sum := 0

	// compute the sum
	for i := 0; i < len(subarr); i++ {
		sum += subarr[i]
	}

	// write the sum into channel
	ch <- sum
}

func main() {
	// communication channel
	ch := make(chan int)

	// supposedly large array of integers
	arr := [19]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19}

	// calculate the mid-point for splitting
	mid := int(len(arr) / 2)

	totalSum := 0

	// create goroutines to
	go sumSubarray(ch, arr[0:mid])
	go sumSubarray(ch, arr[mid:])

	// collect the results
	totalSum += <-ch
	totalSum += <-ch

	// print the results
	fmt.Println("Total sum: ", totalSum)
}
```

## Takeaways

Concurrency in Go makes handling multiple tasks simple, with *goroutines* and *channels*. Goroutines let you run functions concurrently in a memory-efficient way, while channels help with friction-less, controlled communication between these goroutines. 
                     
Moreover, slicing and channel synchronization, solves problems involving multiple tasks—like splitting and summing arrays. The Go runtime manages much of this behind the scenes.
     
> I hope you picked up a thing or two from this one, happy learning!!
                     

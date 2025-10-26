---
title: "Go Interfaces: A Beginner's Guide"
excerpt: "A simplified guide to understanding interfaces in Go, their purpose, how they enable polymorphism, and how to use them effectively."
date: 2025-02-22
tags:
  - go
  - interfaces
  - polymorphism
  - beginner
author: "Hamza Mateen"
draft: false
toc: true
layout: "blog"
readingTime: 5
slug: "go-interfaces-guide"
coverImage: "/images/go-interfaces.png"
---


### Introduction

An interface at its best, is an **interaction point** where two different _systems_ meet in order to interact with each other.  
Interfaces in Go are none different, they can be understood as interaction points for two or more apparently unrelated types.

The **interface** in Go language is a built-in construct to introduce custom types. Interfaces are _abstract_ which means that we are not allowed to instantiate them. For some type to be compatible with an interface, it has to implement the methods specified in the interface. This process is referred to as **satisfying** of the interface.

In short, the primary purpose of their existence is a behavioral one; they enable us to treat different types as the same as long as these types share some common behavior.

In Go, an interface can be declared as follows:

```
type Duck interface {
    // declare methods here ... 
}
```

The name can be anything apart from _Duck_ but there's a reason I chose that name, let's dig deeper.

### The Duck Typing Test

Go is a _strongly typed_ language. This means we cannot have a type _opt in_ to hold different type of data once it has been declared. That gives huge benefits in terms of code predictability, static analysis and, just-in-time debugging, but it comes with a cost - _we no longer have type flexibility_.

Strong nature of a language prevents the programmer from inventing unanticipated types. There's a set of types built into the language that are to be utilized and hence, there's no middle ground between types. It is either one way or the another.  
Either you got a string or you have an int; nothing in between... or is it?

Introducing _Duck Typing_... test, it goes like this:

> If it walks like a duck and it quacks like a duck, then it must be a duck - [Wikipedia](https://en.wikipedia.org/wiki/Duck_typing)

The crux of _Duck Typing_ principle is the idea that code _does not_ care whether an object is a duck but rather it concerns with the fact that it _quacks_ or not.

Let's make sense out of it with some code. We define the glorious Duck interface as before:

```
type Duck interface {
    // our methods go here
}
```

We declare a type interface named _Duck_. The Duck interface characterizes a _contract_ for any subsequent types that satisfy it. But as of now, it is quite vague since there are no rules defined here. This is where _methods_ come in handy. Methods describe the kind of behavior a type would need to exhibit in order to satisfy the interface.

We know that a duck should be able to _walk_ and _quack_. Let's add these two methods to our interface;

```
type Duck interface {
    Walk()
    Quack()
 }
```

We have added the required rules of the contract. It is now job of the type in question to implement these methods to be given the perk of being categorised as a duck.

An important distinction to note here is that this mechanism in Go is called **Structural Typing** as it has a static type system.  
**Duck Typing** is more suited for languages which are dynamic in nature such as _Python_ and/or _Elixir_, etc. But nonetheless, goal of both design patterns is the same.

### Implementing the Interface

There are countless types of ducks out there with varied attributes, such as color, size or flying capabilities. Let's take for example, the Mandarin and Grebes.

Grebes are primarily found in lakes. They are known for their diving abilities. Mandarin ducks prefer wooded areas near ponds. They often like to remain static. That's quite an array of differences, right?

let's create the `structs` named after these types of ducks;  
For Grebe:

```
type Grebe struct {
    isGoodDiver bool
}
```

similarly, for Mandarin:

```
type MandarinDuck struct {
    size int
    habitat string
}
```

These are _different_ types by definition, we cannot assign their instances to one another.

What if we have a function that expects just _any_ type of duck regardless of their differences? _Mandarin_ and _Grebe_ are both reasonable candidates. After all, they are ducks. They can _walk_ and _quack_.  
Essentially what we need here is some sort of _polymorphic behavior_.

### Interfaces for Polymorphism

_The ability of an object to behave in multiple ways is called Polymorphism_. It is evident from our discussion that we need both duck types to exhibit the behavior of a general duck. Let us suppose there is a function that expects an object which satisfies the Duck interface:

```
func DuckAcceptor(duck Duck) {
   duck.Walk()
   duck.Quack()
}
```

As of now, we cannot pass in the instances of _Grebe_ or _MandarinDuck_. This can only be achieved once we implement the _Duck_ interface on both duck types.

The basic syntax to implement the methods of an interface in Go is pretty similar to defining normal functions. We just provide an object of the type which we intend to implement the interface on.  
For _MandarinDuck_:

```
func (md MandarinDuck) Walk() {
    fmt.Println("This is MandarinDuck walking... I walk simple!!!")
}

func (md MandarinDuck) Quack() {
    fmt.Println("quack quack!!")
}
```

similarly, for Grebe type:

```
func (g Grebe) Walk() {
    fmt.Println("This is Grebe duck walking!")
}

func (g Grebe) Quack() {
    fmt.Println("quuaackkkkk quuaaackkkk!!")
}
```

Now, we can pass an object of any of these types to our `DuckAcceptor(d Duck)` function. `d` is now a generic duck type. The is the power polymorhism. A single type automatically binding to an appropriate _sub type_ without programmer's intervention. And we achieved this through interfaces.

let's see how this affects our program. We instantiate both structs,

```
var grebe Grebe
var mandarinDuck MandarinDuck 
```

Now if we call our `DuckAcceptor(d Duck)` function twice passing an object of each type respectively, we see that the `Walk()` and `Quack()` functions respond to the internal type change.

```
DuckAcceptor(grebe)
fmt.Println()
DuckAcceptor(mandinDuck)
```

We can already envision the output, that is:

```
This is Grebe duck walking!
quuaackkkkk quuaaackkkk!!

This is MandarinDuck walking... I walk simple!!!
quack quack!!
```

Obvious... right?

Summarizing it, interfaces are a powerful tool that enable us to achieve polymorphism in our code. They help us group related functionality together and make our code reusable.

### The Empty Interface

There's only piece to the puzzle that remains to be solved and that is the **Empty Interface**.

Go is primarily a statically typed language which means that every type needs to be known at _compile time_ before the code is executed. This prevents type flexibility. Luckily, Go allows the programmer to make assumptions about types and this can be achieved through the empty interface, so what's an empty interface?

_An interface which has zero contract protocols(methods) is called empty interface_

let's quickly define one:

```
type EmptyInterface interface{}
```

What good does it do? glad you asked! every type in go automatically implements the empty interface because it does not put any rules(methods) in place to be _satisfied_ with. So we can basically start to qualify even our simple data types such as, int or string as `interface{}`. let's see an example from the official docs:

```
var any interface{}

any = 42
describe(any)

any = "hello"
describe(any)
```

The output is:

```
(42, int)
(hello, string)
```

As we can see, `any` variable can take on _any_(no pun intended) type of data. The `describe()` is a custom function that describes type of information of the passed in arguments.

Empty interfaces are of great use when creating Go packages and libraries. There are some pitfalls or caveats to this approach such as the need of constant _Type Assertions_ but that's a story for some another time.

_I hope you had a very good time here and picked up a thing or two. Happy Go-ing!!!_

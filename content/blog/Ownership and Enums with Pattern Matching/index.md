---
title: "Ownership and Enums with Pattern Matching"
excerpt: "An explanation of Rust's ownership system and how enums and pattern matching provide expressive, memory-safe control flow."
date: 2025-02-21
tags:
  - rust
  - ownership
  - pattern matching
  - enums
  - programming
author: "Hamza Mateen"
draft: false
toc: true
layout: "blog"
readingTime: 7
slug: "ownership-enums-pattern-matching"
coverImage: "/images/rust-ownership.png"
---

Ownership Challenge: Swap Values
Write a function that takes two mutable references and swaps their values.

```rust
fn swap_values<T>(a: &mut T, b: &mut T) {
    // implement swapping logic
}
```

Enum and Pattern Matching Challenge: Traffic Light
Define an enum representing the states of a traffic light (Red, Yellow, Green). Write a function that simulates the traffic light by returning the next state given the current state.

rust
```rust
enum TrafficLight {
    Red,
    Yellow,
    Green,
}

fn next_traffic_light_state(current_state: TrafficLight) -> TrafficLight {
    // implement logic to return the next state
}
```
Ownership and Borrowing Challenge: String Concatenation
Write a function that concatenates two strings and returns a new string. Make sure the function takes ownership of the input strings.

rust

```rust
fn concatenate_strings(s1: String, s2: String) -> String {
    // implement string concatenation logic
}
```
Enum and Pattern Matching Challenge: Arithmetic Operation
Create an enum representing basic arithmetic operations (Add, Subtract, Multiply, Divide). Write a function that performs the specified operation on two numbers.

rust
```rust
enum ArithmeticOperation {
    Add,
    Subtract,
    Multiply,
    Divide,
}
```
```rust
fn perform_operation(a: f64, b: f64, operation: ArithmeticOperation) -> f64 {
    // implement arithmetic operation logic
}```

Ownership Challenge: Vector Transformation
Write a function that takes ownership of a vector of integers, squares each element, and returns a new vector with the squared values.

rust
```rust
    fn square_elements(input: Vec<i32>) -> Vec<i32> {
        // implement vector transformation logic
    }
```
Feel free to adapt these challenges and experiment further. They cover various aspects of ownership, borrowing, enums, and pattern matching in Rust. Happy coding!

**Advanced Compiler Stuff:** 
Informally, reification is often referred to as "making something a first-class citizen" within the scope of a particular system. Some aspect of a system can be reified at *language design time*, which is related to reflection in programming languages. It can be applied as a stepwise-refinement at system design time. 

Functional programming languages based on lambda-calculus reify the concept of a procedure abstraction and procedure application in the form of Lambda expression.

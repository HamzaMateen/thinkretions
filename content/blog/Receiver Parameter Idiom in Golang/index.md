---
title: "Receiver Parameter Idiom in Golang"
excerpt: "Exploring method receivers in Go, including value vs. pointer receivers, common idioms, and when to use each for cleaner APIs."
date: 2025-02-22
tags:
  - go
  - idioms
  - receiver
  - methods
  - pointers
author: "Hamza Mateen"
draft: false
toc: true
layout: "blog"
readingTime: 5
slug: "receiver-parameter-idiom-go"
coverImage: "/images/go-receivers.png"
---

I came across this line of code in a GoLang code base today:

```
func (us UserStore) GetUsers(w http.ResponseWriter, r *http.Request) { 
    // implementation 
    // making use of `us` object/variable
}
```

and my honest reaction was ... _humm!_

To me, this wasn't new but also something that I haven't paid much attention to either. I had just realized that the variable `us` is actually not in the argument list.

If we take look at the above snippet, the `us` variable is being passed separately to the function before the function name, and this is what refers to as **Receiver Parameter Idiom** in Go, where the **idiom** bit signifies a common or established pattern.

> In Go, a receiver parameter is a special parameter in a method declaration that allows a type to define methods that can be called on its instances.

This pattern is primarily used to tie functions to a _specific_ type, which in this case happens to be `UserStore`. It is less strict imitation of an _Object Oriented Programming_ approach called **Encapsulation**, which means ensuring the privacy of the content of an object.

Since **Go** does not qualify as purely Object Oriented language, it doesn't have any explicit _constructs_ for data hiding like **private** or **protected** which are present in languages like _C++_ and _Java_, for example. So the attempt at _encapsulation_ is not an enforcement but rather a suggestion.

The associating of methods to a certain type also introduces **modularity** because functionality related to certain type is closely coupled to that type only, and the functions defined that type basically act as an extensional interface towards outside world.

_Let's see a short example,_

```
package main
import "math"

// Let's define a struct type Point which represents a 2D point.
type Point struct {
    x, y float64 
}

// constructs a point and returns it
// acts as a constructor
func NewPoint(x, y float64) Point {
    return Point{x, y}  
}

func (p *Point) Distance(q Point) float64 {
    dx := p.x - q.x
    dy := p.y - q.y
    return math.Sqrt(dx*dx + dy*dy)
}
```

I have defined a `Point` struct and a couple of methods surrounding its usage. The `NewPoint` constructs a new Point object and returns it while the `Distance` method calculates the Euclidian distance between two such points. If we look closely, the later one uses _receiver parameter_ which means we can call this method directly on a `Point` object or a pointer to its object. Choosing between passing an object by value or by reference to a method depends on whether we want to modify the original object or if the object size is relatively larger, making the copy operation an expensive choice.

in the **main** function:

```
package main
func main() {
    // create a Point without using NewPoint method
    origin := Point{0,0}
    
    // Create instances of the Point type using the constructor
    p := NewPoint(1, 2)
    q := NewPoint(4, 6)

    // Use the Distance method to calculate the distance between points
    distance := p.Distance(q)
    fmt.Printf("Distance between points: %f\n", distance)
}
```

In the main function, we create two points and then calculate the distance between them.

This is is the essence of associating a method with an object, we get to use . (dot) syntax call this function which is a bit more OOPs like. It is also evident that the `NewPoint` is not inherently connected to the `Point` because doesn't receive its object unlike `Distance`. This can be confirmed by the first line of the `main` function where we create a point without involving the `NewPoint` method. This isn't to say that we can't do the same for finding the distance without using `Distance` method rather to convey the fact that the user of this function will immediately recognize the connection between the `Point` and `Distance`.

Moreover, in case if were importing these functions from and outside package or module, the second method would automatically seem to be more closely related to the `Point` because it already operates on its object.

**Thanks for reading!**

---
layout: post
title: "Structs, interfaces, and type assertions for beginners"
description: ""
category: 
tags: [go, types, type assertions]
---
{% include JB/setup %}

## Problem ##

Struct types, interface types, and type assertions in Go were becoming as much of a noodle scratcher as strings symbols were in Ruby. So I am writing the blog post that I hope might help out the next Gopher.

## The Dilly ##

### Basic Struct ###

{% highlight go %}
package person 

type Person struct {
  Name string
  Age int
  ssn string
}

func (p Person) Greet() {
  fmt.Println("Hello, I am", p.Name)
}

func (p Person) Walk(m int) {
  fmt.Printf("I would walk %d mooyels.", m)
}

func BuildAPerson(name string, age int, ssn string) Person {
  return Person{name, age, ssn}
}

func main() {
  var person Person
  person = Person{"Ted", 34, "111-222-3333"}
}
{% endhighlight %}


I declared a struct with two exported fields, __Name__ and __Age__, as well as a non-exported field, __ssn__. If you are new to Go, think of exported fields as public variables, unexported fields are private variables. If you are coding in the same package as a struct, you may set its privates, however if you are not in the same package, do not try and touch another struct's privates. Plain and simple.

I have created a method -- _methods have receivers, functions don't!_ -- allowing a person to give a greeting. I have created a function to build a person by passing 3 values.

Take notice of the __BuildAPerson__ function. _The name is capitalized, therefore is is exported_. This makes the function useful for other packages. Other packages may call this function to build a person, including an __ssn__ string. 

Why do we need a separate __BuildAPerson__ function? Well, because __ssn__ is unexported, thus you can't even attempt to set it -- event whilst instantiating, for Pete's sake -- from a external package. From an external package you could pass in the Name and Age using the literal, but that is it. So, if you have unexported fields in your structs, make sure you export a builder method if need be.

In the main function, a Person has been initialized ("person"), with my current info, even the __ssn__.

We good?

### The Basic Interface ###

{% highlight go %}
type Greeter interface {
  Greet()
}
{% endhighlight %}

A __method set__ is all of methods associated with a struct or the methods declared between the "squiggles" '{...}' of an interface.

Greeter has a method set with a single method: Greet. __Greet__ has a method signature that accepts zero arguments, sometimes referred to as an __arity of zero__. Greet returns nothing. __Very important__.

_Interfaces in Go are implicit, they are implied, there is no explicit connection betwixt structs and interfaces. It's like being a part of The Stonecutters._

"Hey struct, you want to be part of our society? Just wear this ring (this method) and you are in! No one even has to know that you are part of society, its just between you and us."

Got it? Yeah feel me?

This is called duck typing.  I digress.

If you want a struct, Person, for one, to be a Greeter, all the sturct needs to do implement the "Greet" method _with an identical signature_. Again, it is not good enough to simply implement a method named "Greet." To be considered a Greeter, "Greet" must have an arity of zero and return nothing. A noob like me will spend hours trying to figure a way around this to create __generics__. Save your time, there is no getting around this.

## The Empty Interface ##
{% highlight go %}
type Blanky interface {}
{% endhighlight %}

The empty interface is one way to pass any struct to any method or literal. Because the method set is empty, and every struct has zero or more methods, every struct matches this interface. Many Go noobs, including yours truly, got carried away with the idea of using the empty interface to create Ruby-esque "anything goes" arrays, slices, etc. This is bad for performance, bad for maintenance, bad for bugs, bad bad bad. Use Go, don't abuse Go.

{% highlight go %}

// Hamburger bad
// Milkshake bad
// Empty interfaces, bad. Ooops! I said it.
foo := []interface{}{"Ted", 32, Car{"Honda"}, someChannel}
{% endhighlight %}

## Putting it all together ##

So Person has 2 methods in its method set, Greet and Walk. What happens when I cast my Person as a Greeter and try to walk:

{% highlight go %}

greeter := Greeter(person)
greeter.Greet()
// greeter.Walk(500)   XXX: compile time error

{% endhighlight %}

This will not compile. Go doesn't allow this silly bidness. At compile time, Go will say: "Hey Greeter! Walk isn't in your method set, just what do you think you are doing dawg??"

Whats your rejoinder? 

Well, you say to Go compile: "but I am a Person! And I can walk!"

Go is all: "well make a type assertion to a struct and if you able to walk, then keep on keepin' on!"

{% highlight go %}
// Greet
greeter := Greeter(person)
greeter.Greet()

// Now cast yourself to a walker!
new_person := greeter.(Person)
new_person.Walk(500)   
{% endhighlight %}

So everything is great in the Go world. But what happens in you try to pretend to be something you are not?

{% highlight go %}
type Robot {}
func (r Robot) Greet() {
  // Do nothing
}

func main() {
  robot := Robot{}
  greeter := Greeter(robot)

  // Robot has human envy :(
  new_person := greeter.(Person)  // PANIC AT THE DISCO!!!

{% endhighlight %}

Well, while I tell people "if it compiles, it works!!" the truth more nuanced -- can you believe it? Type assertions are where panics, that is "runtime errors", can occur with frequency. Patterns exist to deal with variable type assertions like case statements, below. My experience is that its best to deal with interfaces. Do not assert type just because you don't want to consider the abstraction.

{% highlight go %}
func doSomething(greeter Greeter) {

  switch v := greeter.(type) {
    case Person:
      fmt.Println("I am a human")
    case Robot:
      fmt.Println("I need oil!")
    default:
      fmt.Println("I just greet ")
  }
{% endhighlight %}

## Type Assertion is Costly ##

Many will __assert__ -- see what I did there? -- that type assertion is a costly cast, but the reality is that if its done with prudencei, like at the return/exit/terminus of your method call chain, then who cares?  Having stated this, don't rest of your type assertion laurels! Learn how to use interfaces effectively. Learn how to use abstractions. To learn the Go way is to love the Go way.

In a project I am currently working on, we have had to use type assertions. Wary of the performance cost, I try to capture the relative performance hit using the benchmark script, below. YMMV, but it was good enough for me to be all "DGAF, YOLO!", on our sparse use of type assertions.

### Conclusion ###

Structs, interfaces, and type assertions require getting reacquainted with static typing if you've been hanging out in the weak & dynamic types world were generics rule and anything goes. I am looking at you, Javascript and Ruby. With some knowledge of types and interfaces you can hone your skills of abstraction, and live a happy and productive Go existence. 

I hope this blog post has been a beneficial to someone.

In you are in Chicago, come hang with us at meetup.com/ChicaGoLang

For more reading http://research.swtch.com/interfaces

{% highlight go %}
/*

  This is a very casual look at the speeds of type assertions:

  Baseline returns what is passed in
  Greeter returns a Person asserted from a Greeter interface
  Empty returns a Person asserted from an empty interface

  If you are a Rubyist, like myself, ~100ns per op is delightful. If you are used to real programming, 
  this may turn your stomach. For my purposes, it is more than acceptable.

  My recommendation: don't assert type willy nilly. Pass interfaces wherever possible. 
  Only assert type when you need to make use of methods not available via an interface. 
  Even then, reconsider your code. Ask: Can I do better?

  For more reading http://research.swtch.com/interfaces
  ====

$ go test -bench .

  After a couple of iterations, the differences were within 1ns/op

PASS
BenchmarkBaseline               100000000  10.4 ns/op
BenchmarkGreeter                20000000  111 ns/op
BenchmarkEmpty                  20000000  112 ns/op
BenchmarkGreeterNoFunctionCall  100000000  17.2 ns/op
BenchmarkEmptyNoFunctionCall    100000000  15.9 ns/op

*/

package benchy

import (
  "testing"
  "fmt"
)

type Blanky interface{}

type Greeter interface {
  Greet()
}

type Person struct {
  Name string
  Age int
}

func (p Person) Greet() {
  fmt.Println("Hello, I am", p.Name)
}

// Type assertion from interfaces
func type_assert_greeter(g Greeter) Person{
  return g.(Person)
}

func type_assert_empty(i interface{}) Person {
  return i.(Person)
}

func baseline(p Person) Person {
  return p
}

func BenchmarkBaseline(b *testing.B) {
  foo := Person{"Ted", 34}

  for i := 0; i < b.N; i++ {
    baseline(foo)
  }
}

func BenchmarkGreeter(b *testing.B) {
  foo := Person{"Ted", 34}

  for i := 0; i < b.N; i++ {
    type_assert_greeter(foo)
  }
}

func BenchmarkEmpty(b *testing.B) {
  foo := Person{"Ted", 34}

  for i := 0; i < b.N; i++ {
    type_assert_empty(foo)
  }
}

func BenchmarkGreeterNoFunctionCall(b *testing.B) {
  foo := Person{"Ted", 34}
  greeter :=  Greeter(foo)

  for i := 0; i < b.N; i++ {
    _ = greeter.(Person)
  }

}

func BenchmarkEmptyNoFunctionCall(b *testing.B) {
  foo := Person{"Ted", 34}
  blanky := Blanky(foo)

  for i := 0; i < b.N; i++ {
    _ = blanky.(Person)
  }
}
{% endhighlight %}

---
layout: post
title: "Go Refresher: Slices, Arrays"
description: ""
category: 
tags: []
---
{% include JB/setup %}

There is nothing quite like a bout of imposter syndrome to bring enough pain to write a post about language primitives.

I was assisting some coworkers on a Go project this week, I haven't worked in Go for a lil bit. I damn near forgot everything concerning arrays and slices. So, what better way to re-inforce the knowledge in the noodle than to write a blog post.

### Arrays ###

Go's arrays are true to the memory/low-level game. An array uses an allocated block of contiguous memory.
Assuming a 8-byte integer (64-bit), an array holding 1, 2, 3, 4 and 5 (in order) might look like this:

Memory Address | Value
-------: | :-----:
0x0000 | 1
0x0008| 2
0x0010| 3
0x0018| 4
0x0020| 5

[Go Playground](https://play.golang.org/p/rmXskg-Tln)
{% highlight go %}
// https://play.golang.org/p/rmXskg-Tln
package main

import (
	"fmt"
	"reflect"
)

func main() {
	ary := [5]int{1, 2, 3, 4, 5}

	t := reflect.TypeOf(ary)

	fmt.Printf("type: %s\nvalue: %v\n\n", t, ary)

	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range ary {
		fmt.Printf("%v\t%d\t%d\n", &ary[i], i, j)
	}
}

/*
Output:

type: [5]int
value: [1 2 3 4 5]

Address         Idx     Val
0xc208036180    0       1
0xc208036188    1       2
0xc208036190    2       3
0xc208036198    3       4
0xc2080361a0    4       5

*/
{% endhighlight %}

### Out of bounds ###

There are a few invariants with arrays. One of them is there size. An array can almost never grow. The above array is 5 integers in size, or capacity. It can't grow to be 6, because it is bounded the physical memory it was assigned by the kernel, via lower level calls.

If you were to attempt to access `ary[6]`, from the preceding example, you would receive an error:  
`"...invalid array index 6 (out of bounds for 1-element array)"`. More on this gem later. 
[Go Playground](https://play.golang.org/p/GvCYiZmc4Y)

### Adding new elements to an array ###

To append elements to this array, we must:

1. Request the allocation of a new block of memory that has enough space to hold the existing elements of the array plus the new elements
2. Copy the existing elements from the existing block of memory to the new block of memory
3. Add new elements to new block of memory

[Go Playground](https://play.golang.org/p/2owFMGzlsa)
{% highlight go %}
// https://play.golang.org/p/2owFMGzlsa

package main

import (
	"fmt"
	"reflect"
)

func main() {
	ary := [5]int{1, 2, 3, 4, 5}
	fmt.Printf("Adding some thangs to %v\n", ary)

	newAry := [len(ary) + 5]int{}
	for i := 0; i < len(ary); i++ {
		newAry[i] = ary[i]
	}
	for i := len(ary); i < len(newAry); i++ {
		newAry[i] = i * 100
	}

	t := reflect.TypeOf(newAry)

	fmt.Printf("type: %s\nvalue: %v\n\n", t, newAry)

	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range newAry {
		fmt.Printf("%v\t%d\t%d\n", &newAry[i], i, j)
	}
}

/*

  OUTPUT:

  Adding some thangs to [1 2 3 4 5]
  type: [10]int
  value: [1 2 3 4 5 500 600 700 800 900]

  Address		Idx	Val
  0xc208020190  	0	1
  0xc208020198  	1	2
  0xc2080201a0  	2	3
  0xc2080201a8  	3	4
  0xc2080201b0  	4	5
  0xc2080201b8  	5	500
  0xc2080201c0  	6	600
  0xc2080201c8  	7	700
  0xc2080201d0  	8	800
  0xc2080201d8  	9	900
*/
{% endhighlight %}

Are we having fun yet?!

Somewhere between the C or Assembly array (keep it trill) and the Ruby or Python Array (more of a linked list with array and queue functionality), there is tha slice!

### Slices ###

A slice is an abstraction of an array. A slice gives us some of the flexible we need, along with the speed and memory efficiency that we crave!

Its important to drill into your dev-mind that the slice is not a memory allocated data structure unto itself. It points to an underlying arary, that is: a contiguous, finite, block of memory. One can create an infinite number of slices that all point to the same underlying array. With great power, comes great responsibility.

Slices are created using a great many methods:  

  - from an array
  - from another slice
  - `make`
  - slice literal

#### From an array or existing slice ####

"Slicing" an array or existing slice involves using brackets along with a combination of start and end points. The start point is inclusive, the end point is exclusive. If the start index is omitted it is defaulted to 0, if the end index is omitted it defaults to the max length of that which is sliced.
{% highlight go %}

foo := []{1, 2, 3, 4}

_ = foo[:]     // [1, 2, 3, 4]  everything
_ = foo[0:0]   // [] nothing
_ = foo[1:2]   // [2] 1 element
_ = foo[1:3]   // [2, 3] 2 element elements
_ = foo[1:]    // [2, 3, 4] from here to eternity, same as [1:len(foo)]
_ = foo[:2]    // [1, 2] all to thither, same as [0:2]

{% endhighlight %}

No, on with the show:

{% highlight go %}
// https://play.golang.org/p/tVwY9m90Et
package main

import (
	"fmt"
	"reflect"
)

func main() {
	ary := [3]rune{'c', 'a', 'r'}

	t := reflect.TypeOf(ary)

	fmt.Println("[ary]")
	fmt.Printf("type: %s\nvalue: %c\n\n", t, ary)

	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range ary {
		fmt.Printf("%v\t%d\t%c\n", &ary[i], i, (j))
	}

	fmt.Println("\n\n[s1]")
	// Slice derived from entire array
	s1 := ary[:]
	t = reflect.TypeOf(s1)
	fmt.Printf("type: %s\nvalue: %c\n\n", t, s1)

	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range s1 {
		fmt.Printf("%v\t%d\t%c\n", &s1[i], i, j)
	}

	fmt.Println("\n\n[s2]")
	// Slice derived from entire array
	s2 := ary[0:1]
	t = reflect.TypeOf(s2)
	fmt.Printf("type: %s\nvalue: %c\n\n", t, s2)

	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range s2 {
		fmt.Printf("%v\t%d\t%c\n", &s2[i], i, j)
	}

	fmt.Println("\n\n[s3]")
	// Slice derived from last character of s1
	s3 := ary[len(s1)-1:]
	t = reflect.TypeOf(s3)
	fmt.Printf("type: %s\nvalue: %c\n\n", t, s3)

	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range s3 {
		fmt.Printf("%v\t%d\t%c\n", &s3[i], i, j)
	}

	// Now the fun

	s3[0] = 't'
	fmt.Printf("\n[s3] = %c", s3)
	fmt.Printf("\n[ary] = %c", ary)
	fmt.Printf("\n[s1] = %c", s1)

	s2[0] = '語'
	fmt.Printf("\n[s2] = %c", s2)
	fmt.Printf("\n[ary] = %c", ary)
	fmt.Printf("\n[s1] = %c", s1)

	ary[0] = 'B'
	fmt.Printf("\n[ary] = %c", ary)
	fmt.Printf("\n[s2] = %c", s2)
	fmt.Printf("\n[s1] = %c", s1)
}

/*
  OUTPUT:


[ary]
type: [3]int32
value: [c a r]

Address		Idx	Val
0x10436180	0	c
0x10436184	1	a
0x10436188	2	r


[s1]
type: []int32
value: [c a r]

Address		Idx	Val
0x10436180	0	c
0x10436184	1	a
0x10436188	2	r


[s2]
type: []int32
value: [c]

Address		Idx	Val
0x10436180	0	c


[s3]
type: []int32
value: [r]

Address		Idx	Val
0x10436188	0	r

[s3] = [t]
[ary] = [c a t]
[s1] = [c a t]

[s2] = [語]
[ary] = [語 a t]
[s1] = [語 a t]

[ary] = [B a t]
[s2] = [B]
[s1] = [B a t]

*/
{% endhighlight %}

Things to note:

- Slice syntax is similar to an array, except it misses the length
- New memory is not allocated for elements of a slice, the slice points to a collection of elements of an underlying array
- A change to a slice, modifies the underlying memory, which can modify any/all other slices that share that memory

#### Using `make` ####

A few things:

 - Syntax: `make([]T, length int, capacity int)`
 - requires a slice with a type
 - requires a length
 - optionally accepts a capacity, defaults to length

{% highlight go %}
package main

import "fmt"
import "reflect"

func main() {
	slice := make([]rune, 4, 8)
	t := reflect.TypeOf(slice)
	fmt.Printf("\n\ntype: %s\nvalue: %v", t, slice)
	fmt.Printf("\nlength: %d", len(slice))
	fmt.Printf("\ncapacity: %d", cap(slice))

	fmt.Println("\n\nAddress\t\tIdx\tVal")
	for i, j := range slice {
		fmt.Printf("%v\t%d\t%s\n", &slice[i], i, string(j))
	}

}

/*
type: []int32
value: [0 0 0 0]
length: 4
capacity: 8

Address		Idx	Val
0x104342e0	0
0x104342e4	1
0x104342e8	2
0x104342ec	3
*/
{% endhighlight %}

Things to note:

- Slice elements default to zero-value ("" for rune/string, 0 for int, etc)
- The range iterates from 0 to length-1, not capacity
- Is it starting to click?

#### Slice Literal ####

Use Go's literal pattern to create a slice. 
{% highlight go %}
package main

import (
	"fmt"
	"reflect"
)

func main() {
	slice := []rune{'c', 'a', 'r'}

	t := reflect.TypeOf(slice)

	fmt.Println("[slice]")
	fmt.Printf("type: %s\nvalue: %c\n\n", t, slice)
	fmt.Printf("length: %d\ncap: %d\n\n", len(slice), cap(slice))

	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range slice {
		fmt.Printf("%v\t%d\t%c\n", &slice[i], i, (j))
	}
}

/*
  OUTPUT:

[slice]
type: []int32
value: [c a r]

length: 3
cap: 3

Address         Idx     Val
0xc208000150    0       c
0xc208000154    1       a
0xc208000158    2       r

*/
{% endhighlight %}

Things to note:

- The capacity is the length, i.e. the number of elements in the literal
- The underlying array is _anonymized_

### Growing a slice ###

Okay, so now you are cooking with gasoline.

There are two methods to increase the size of a slice:

  - The magical "append"
  - new slice &rarr; copy

#### Append ####

Append takes a few forms:

 - `append(target_slice, element)` &rarr; new_slice
 - `append(target_slice, el1, [el2, ])` &rarr; new_slice
 - `append(target_slice, ...another_slice)` &rarr; new_slice

`append` will return a new slice, the size of target slice plus the number of elements being appended. The target slice is not modifed in place, its length remains the same. *The underlying array is modified.*
[Go Playground](https://play.golang.org/p/wOyfOMWTLH)

{% highlight go %}
// https://play.golang.org/p/wOyfOMWTLH
package main

import (
	"fmt"
)

func printSlice(slice []int) {
	fmt.Println("[slice] =>", slice)
	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range slice {
		fmt.Printf("%v\t%d\t%d\n", &slice[i], i, (j))
	}
	fmt.Println()
	return
}

func main() {
	ary := [3]int{0, 0, 0}
	for i, j := range ary {
		fmt.Printf("%v\t%d\t%d\n", &ary[i], i, (j))
	}
	slice := ary[0:1]

	fmt.Println("[ary] =>", ary)
	printSlice(slice)

	for i := 1; i < 4; i++ {
		_ = append(slice, i*5)
		fmt.Println("[ary] =>", ary)
		printSlice(slice)
	}
}

/*
0xc20804c000    0       0
0xc20804c008    1       0
0xc20804c010    2       0
[ary] => [0 0 0]
[slice] => [0]
Address         Idx     Val
0xc20804c000    0       0

[ary] => [0 5 0]
[slice] => [0]
Address         Idx     Val
0xc20804c000    0       0

[ary] => [0 10 0]
[slice] => [0]
Address         Idx     Val
0xc20804c000    0       0

[ary] => [0 15 0]
[slice] => [0]
Address         Idx     Val
0xc20804c000    0       0
*/
{% endhighlight %}

Look at what happens with we reassign the slice after an append:  
[Go Playground](https://play.golang.org/p/PgLVRw3jrJ)

{% highlight go %}
// https://play.golang.org/p/PgLVRw3jrJ
package main

import (
	"fmt"
)

func printSlice(slice []int) {
	fmt.Println("[slice] =>", slice)
	fmt.Println("Address\t\tIdx\tVal")
	for i, j := range slice {
		fmt.Printf("%v\t%d\t%d\n", &slice[i], i, (j))
	}
	fmt.Println()
	return
}

func main() {
	ary := [3]int{0, 0, 0}
	for i, j := range ary {
		fmt.Printf("%v\t%d\t%d\n", &ary[i], i, (j))
	}
	slice := ary[0:1]

	fmt.Println("[ary] =>", ary)
	printSlice(slice)

	for i := 1; i < 4; i++ {
		slice = append(slice, i*5)
		fmt.Println("[ary] =>", ary)
		printSlice(slice)
	}

	fmt.Println("[ary]")
	for i, j := range ary {
		fmt.Printf("%v\t%d\t%d\n", &ary[i], i, (j))
	}
}

/*
0xc20804c000	0	0
0xc20804c008	1	0
0xc20804c010	2	0
[ary] => [0 0 0]
[slice] => [0]
Address		Idx	Val
0xc20804c000	0	0

[ary] => [0 5 0]
[slice] => [0 5]
Address		Idx	Val
0xc20804c000	0	0
0xc20804c008	1	5

[ary] => [0 5 10]
[slice] => [0 5 10]
Address		Idx	Val
0xc20804c000	0	0
0xc20804c008	1	5
0xc20804c010	2	10

[ary] => [0 5 10]
[slice] => [0 5 10 15]
Address		Idx	Val
0xc208036180	0	0
0xc208036188	1	5
0xc208036190	2	10
0xc208036198	3	15

[ary]
0xc20804c000	0	0
0xc20804c008	1	5
0xc20804c010	2	10
*/
{% endhighlight %}

**Note that the original array was left behind.** Like a hermit crap, the slice had to find a new home in memory after its elements outgrew the size its original array.

Things to Note:
 - Append adds the element(s) to a new slice, that is a copy of the target slice given as the first argument, and the underlying array.
 - Append returns a new slice. READ: Append will not change the slice, given as the first argument, in-memory.
 - Append will create a new underlying array if capacity runs out in the original array runs out

#### New Slice &rarr; Copy ####

So `append` is all fine and dandy when you have to add elements *to the end* of a collection, but what about random inserts? Oh boy, you are in too deep! Turn around!

Lets assume the slice hasn't hit maximum capacity of the underlying array. Inserting is trivial:
{% highlight go %}
  ...
  slice := make([]int, 500, 1000)
  ...
  newSlice := []int{1, 2, .. 50 }
  // need to insert "newSlice" elements at index 100 of original "slice"
  idx = 100

  // grow the slice by the number of new elements
  slice = slice[:len(slice) + len(newSlice)]  

  // shift all elements from the insertion index over to the right to make space for the new elements
  copy(slice[idx+len(newSlice):], slice[idx:]) 

  // insert new elements at insertion index
  copy(slice[idx:], newSlice) 
  ...

{% endhighlight %}

Okay thats easy! But what about when you have maxed out the capacity of the underlying data structure, you know... when there is no space in that there array!?
.. Can you just grow the slice like you might expect?

{% highlight go %}
package main

import (
	"fmt"
)

func main() {
	ary := [3]int{0, 1, 4}
	slice := ary[:]

	// Lets insert a 3 into index 2
	// Step 1. Grow the slice
	slice = slice[:len(slice)+1]

	fmt.Println(slice)
}

/*
  OUTPUT:

  panic: runtime error: slice bounds out of range

*/
{% endhighlight %}

The answer: No!

While the slice needs to be just one element bigger, the underlying array isn't big enough, and we don't have the power to extended the block of memory allocated for our array. We need to allocate a bigger chunk of memory. This requires using the `make` command:

{% highlight go %}
package main

import (
	"fmt"
)

func main() {
	ary := [3]int{1, 2, 4}
	slice := ary[:]

	// Lets insert a 3 into index 2
	val := 3
	idx := 2

	// Step 1. Create a new slice, increasing the length by 1 for the new element. Give the new slice a nice sized capacity for future growth!
	newSlice := make([]int, len(slice)+1, len(slice)*2)

	// Step 2. Copy the elements, index 0 up to the insertion point, from the original slice to the new slice
	copy(newSlice[:idx], slice[:idx])

	// Step 3. Copy the elements, start with the index of the insertion point to the end of the slice, from the original slice to the new slice. However, in the new slice, shift the insertion point to the right by 1 to make room for the new element.
	copy(newSlice[idx+1:], slice[idx:])

  // Step 4. Put that value in proper insertion point!
	newSlice[idx] = val

	fmt.Println(newSlice)
}

/*
  OUTPUT:

  [1 2 3 4]

*/
{% endhighlight %}

You are now free to continue randomly inserting values using the method above. that is, until you hit capacity, again. Capacity algorithms are a topic for another day, however they are very import to the speed, efficiency and resource consumption of your application.

### Summary ###

Notes on arrays:  

 * A array is a contiguous block of physical memory reserved for a variable. 
 * A array's length is fixed, thus its capacity is fixed.

Notes on slices:   

 * is an abstraction tied to an underlying array.
 * capacity is equal to the length of the underlying array, and thus indirectly tied to the physical allocation of a block of memory.
 * length is a _logical limit/size_ set during its creation, either by make, literal, or "slicing" an existing slice or array.
 * length, and capacity if need, be can grown _automagically_ using the "append"
 * If a slices has grown to fill the underlying array, its elements -- like a hermit crab -- will need to find a new home in memory,  if the collection wants to continue to grow by any means other than the magic "append" function (i.e. random insert)

*Special note:
 In Go you do not pass arrays, you pass slices. So get comfortable with everything in the blog post before moving forward ;)*

### Further Reading ###
{% highlight go %}
{% endhighlight %}


[Go Slices: usage and internals](http://blog.golang.org/go-slices-usage-and-internals)  

[The mechanics of 'append'](http://blog.golang.org/slices)

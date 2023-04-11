# Golang Data Structure

There are several basic data structures in computer programming. Some
common ones include:

* `Array`
* `Slices`
* `Maps (dictionaries)`
* `Structs`
* `Linked Lists`
* `Stacks`
* `Queues`
* `Trees`

Let's go over some examples.

## Examples

### Arrays:

Arrays are fixed-size sequences of elements of the same type. They are
useful when you need to store a known number of elements.

```go
package main

import "fmt"

func main() {
  var arr [3]int // Declare array with size 3 of type int
  arr[0] = 10
  arr[1] = 20
  arr[2] = 30

  fmt.Println(arr)
}
```

### Slices:

Slices are dynamic-size sequences of elements of the same type. They are
useful when you need to store a varying number of elements.

```go
package main

import "fmt"

func main() {
  slice :=make([]int, 0) // Declare a slice of type int
  slice = append(slice, 10, 20, 30)

  fmt.Println(slice)
}                                     @ScriptAlchemist
```

### Maps (Dictionaries):

Maps are unordered collection of key-value pairs. They are useful for
storing and retrieving data based on keys.

```go
package main

import "fmt"

func main() {
  myMap := make(map[string]int) // Declare a map with string key and int value
  myMap["apple"] = 5
  myMap["banana"] = 10

  fmt.Println(myMap)
}
```

### Structs:

Structs are custom data types that can group fields with different data
types. They are useful for representing complex data structures.

```go
package main

import "fmt"

type Person struct {
  Name string
  Age  int
}

func main() {
  person := Person{Name: "John", Age: 30}

  fmt.Println(person)
}
```

### Linked Lists:

Linked Lists are dynamic data structures where each element (node)
points to the next one. They are useful for fast insertion and deletion
of elements.

```go
package main

import "fmt"

type Node struct {
  Value int
  Next  *Node
}

func main() {
  node1 := &Node{Value: 10}
  node2 := &Node{Value: 20}
  node1.Next = node2

  fmt.Println("First node:", node1.Value)
  fmt.Println("Second node:", node1.Next.Value)
}
```

### Stacks:

Stacks are a linear data structure that follow the LIFO (Last in first
out) principle, i.e., the last element added to the stack is the first
one to be removed.

```go
package main

import "fmt"

type Stack []int

func (s *Stack) Push(v int) {
  *s = append(*s, v)
}

func (s *Stack) Pop() int {
  res := (*s)[len(*s)-1]
  *s = (*s)[:len(*s)-1]
  return res
}

func main() {
  var stack Stack
  stack.Push(10)
  stack.Push(20)
  stack.Push(30)

  fmt.Println(stack.Pop())
  fmt.Println(stack.Pop())
}
```

### Queues:

Queues are a linear data structure that follows the FIFO (First in first
out) principle, i.e., the first element added to the queue is the first
one to be removed.

```go
package main

import "fmt"

type Queue []int

func (q *Queue) Enqueue(v int) {
  *q = append(*q, v)
}

func (q *Queue) Dequeue() int {
  res := (*q)[0]
  *q = (*q}[1:]
  return res
}

func main() {
  var queue Queue
  queue.Enqueue(10)
  queue.Enqueue(20)
  queue.Enqueue(30)

  fmt.Println(queue.Dequeue())
  fmt.Println(queue.Dequeue())
}
```

### Trees:

Trees are a hierarchical data structure where each node has a value and
a list of child nodes. They are useful for organizing data in a
hierarchical manner.

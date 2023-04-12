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
hierarchical manner. This one we will just mention at this time.

## How can we traverse an array?

```go
package main

import "fmt"

func main() {
  numbers := [5]int{1, 2, 3, 4, 5}

  for i := 0; i < len(numbers); i++ {
    fmt.Println(numbers[i])
  }
}
```

I'm coming from a JavaScript background so this looks very similar to
me. The only difference is the removal of the parenthesis around the for
loop initiation. It still accesses the value by the index inside of
numbers.

There is another way that we can loop at well.

```go
func main() {
  numbers := [5]int{1, 2, 3, 4, 5}

  for _, num := range numbers {
    fmt.Println(num)
  }
}
```

If we perform it with a range. We create an overhead for the creation of
the iterator object. The range could be more convenient and readable for
larger arrays.

## Can we iterate over a map?

We can use the `for range` loop.

```go
func main() {
  m := map[string]int{
    "apple": 1,
    "banana": 2,
    "orange": 3,
  }

  for key, value := range m {
    fmt.Printf("key: %s, value: %d\n", key, value)
  }
}
```

This isn't guaranteed on order. If you need to keep the order every
time. You might want to sort them, and iterate over the sorted keys.

```go
func main() {
  m := map[string]int{
    "apple": 1,
    "banana": 2,
    "orange": 3,
  }

  keys := make([]string, 0, len(m))
  for key := range m {
    keys = append(keys, key)
  }
  sort.Strings(keys)

  for _, key := range keys {
    fmt.Printf("Key: %s, value: %d\n", key, m[key])
  }
}
```

What's this fancy piece right here? `keys := make([]string, 0, len(m))`

This line of code creates a new slice of strings called `keys` using the
`make` built-in function

The first argument to `make` is the type of the slice, which is
`[]string` in this case. The second argument is the initial length of
the slice, which is `0` here because we don't know how many keys the map
will have. The third argument is the capacity of the slice which is set
to `len(m)` to ensure that the slice has enough capacity to hold all
the keys of the map.

By initializing the slice with a length of 0 and a capacity of `len(m)`,
we ensure that the slice can grow dynamically as we add keys to it using
the `append` built-in function. This is more efficient than creating a
slice with a fixed length and then resizing it as we add keys.

We use the `range` loop to iterate over the keys of the map and append
each key to the `keys` slice using the `append` function. After the
loop, the `keys` slice contains all the keys of the map in the order
they were iterated over.

## What does a basic BFS look like in Golang?

Breadth-First Search in Go using a binary tree as the data structure.

```go
package main

import "fmt"

// Create a type TreeNode. The *TreeNode is a pointer to point to
// another tree node.

type TreeNode struct {
  Value int
  Left  *TreeNode
  Right *TreeNode
}

func BFS(root *TreeNode) {
  // if the root is nil the you can return. Base case #1
  if root == nil {
    return
  }

  // The queue is an array
  queue := []*TreeNode{root}

  for len(queue) > 0 {
    node := queue[0]
    queue = queue[1:]

    fmt.Println(node.Value)

    if node.Left != nil {
      queue = append(queue, node.Left)
    }
    if node.Right != nil {
      queue = append(queue, node.Right)
    }
  }
}

func main() {
  root := &TreeNode{Value: 10}
  root.Left = &TreeNode{Value: 5}
  root.Right = &TreeNode{Value: 15}
  root.Left.Left = &TreeNode{Value: 2}
  root.Left.Right = &TreeNode{Value: 8}

  BFS(root)
}
```

What is going on here `queue := []*TreeNode{root}`?

It is initializing a new queue as a slice of pointers to `TreeNode`
elements. It sets the initial elements of the queue to the root of the
tree.

Here's a breakdown of the syntax:

* `queue`: variable name for the queue
* `:=`: short variable declaration syntax in Go which declares and
  initializes a variable without specifying its type explicitly.
* `[]*TreeNode`: slice of pointers to `TreeNode` elements
* `{root}`: Composite literal that crates a new slice with a single
  element, which is the `root` pointer.

The queue is used the BFS algorithm to store the tree nodes that need to
be visited in the order they are discovered. The algorithm dequeues
nodes form the front of the queue and enqueues their children in order
to traverse the tree level by level.

### Do I need to use make()?

No, you don't need to use `make()` in this case. When you create a slice
using a composite literal like `[]*TreeNode{root}`, it automatically
allocates the underlying array and initializes the slice to have the
specified elements. In this case, the slice will have a single element
(the `root` pointer).

The `make()` function is used when you want to crate a slice with a
specified length of capacity, but without any initial elements. For
example, if you want to create an empty slice of pointers to `TreeNode`
with a capacity of 5, you would use `make([]*TreeNode, 0, 5)`.

In the BFS example, using a composite literal to create the queue with
the `root` node is more convenient because it initializes the queue with
the starting point of the traversal.

## What about the DFS?

```go
package main

import "fmt"

type TreeNode struct {
  Value int
  Left  *TreeNode
  Right *TreeNode
}

func DFS(node *TreeNode) {
  if node == nil {
    return
  }

  fmt.Println(node.Value)
  DFS(node.Left)
  DFS(node.Right)
}

func main() {
  root := &TreeNode{Value: 10}
  root.Left = &TreeNode{Value: 5}
  root.Right = &TreeNode{Value: 15}
  root.Left.Left = &TreeNode{Value: 2}
  root.Left.Right = &TreeNode{Value: 8}

  DFS(root)
}
```

This one seems a bit smaller in practice. Relying on the stack of the
recursion instead of creating something like the queue from the Breadth
first search.

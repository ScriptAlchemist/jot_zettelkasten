# Sorting Methods and why

Let's talk a bit about the functionality of what happens inside of the
sorts. We can start with the 5 most basic examples of sorts.

---

* `Bubble Sort`: Simple sorting algorithm that repeatedly steps through
  the list, compares adjacent elements, and swaps them if they are in
  the wrong order. The algorithm continues until no swaps are needed
  which indicates that the list is sorted.

  - Pros
    * Easy to implement
    * Works Well for small datasets or when the data is partially sorted

  - Cons
    * Inefficient for large datasets

---

* `Selection Sort`: Another simple sorting algorithm that sorts an array
  by repeatedly finding the minimum (or maximum) element for the
  unsorted part of the array and swapping it with the first unsorted
  element.

  - Pros
    * Easy to implement
    * Performs well for small datasets
    * Performs a fixed number of swaps, making it useful when swap
      operations are expensive.

  - Cons
    * Inefficient for large datasets

---

* `Insertion Sort`: Simple, stable sorting algorithm that builds the
  final sorted array one element at a time. It is much less efficient on
  large datasets than more advanced algorithms such as Quick Sort, Merge
  Sort, or Heap Sort.

  - Pros
    * Easy to implement
    * Efficient for small datasets or when the data is partially sorted
    * Stable sort (maintains the relative order or equal elements)

  - Cons
    * Inefficient for large datasets

---

* `Merge Sort`: Efficient, stable, divide-and conquer sorting algorithm
  that works by dividing the unsorted list into `n` sublists, each
  containing one element, and then repeatedly merging sublists to
  product new sorted sublists until there is only one sublist remaining.

  - Pros
    * Efficient for large datasets
    * Stable sort
    * Has good average and worst-case time complexity (O(n log n))

  - Cons
    * Requires additional memory for merging sublists

---

* `Quick Sort`: Highly efficient sorting algorithm that works by
  selecting a 'pivot' element from the array and partitioning the other
  elements into two groups-those less than the pivot and those greater
  than the pivot. The algorithm then recursively sorts the two groups.

  - Pros
    * Highly efficient for large datasets
    * Often faster in practice that other sorting algorithms like Merge
      Sort and Heap Sort
    * Can be implemented as an in-place sort (requires little additional
      memory)

  - Cons
    * Not a stable sort
    * Worst-case time complexity is O(n^2), but this can be avoided with
      careful implementation

---

* `Heap Sort`: Comparison-based sorting algorithm that uses a binary
  heap data structure. It works by building a max-heap (a complete
  binary tree where each node is greater than or equal to it's children)
  from the input array, then repeatedly extracting the maximum elements
  form the heap and inserting it at the end of the sorted part of the
  array.

  - Pros
    * Average and worst-case O(n long n)
    * Efficient for large datasets

---

These are the 6 most popular sorting algorithms, each with its own
strengths and weaknesses. The choice of the appropriate sorting
algorithm depends on factors such as the dataset size, whether the data
is partially sorted, the importance of the algorithm's stability, and
the available memory.

Now let's break them down a bit more. Let's dig into some code.

## Let's code

Each of these examples always start with the same piece below.

```go
package main

import "fmt"
```

### Bubble Sort

![Bubble Sort](../../images/go_algos/bubble_sort_graphic.png)

```go
func bubbleSort(arr []int) {
  n := len(arr)
  for i := 0; i < n-1; i++ {
    for j := 0; j < n-i-1; j++ {
      if arr[j] > arr[j+1] {
        arr[j], arr[j+1] = arr[j+1], arr[j]
      }
    }
  }
}

func main() {
  arr := []int{64, 34, 25, 12, 22, 11, 90}
  bubbleSort(arr)
  fmt.Println("Sorted array is:", arr)
}
```

### Selection Sort

```go
func selectionSort(arr []int) {
  n := len(arr)
  for i := 0; i < n-1; i++ {
    minIdx := i
    for j := i + i; j < n; j++ {
      if arr[j] < arr[minIdx] {
        minIdx = j
      }
    }
    arr[i], arr[minIdx] = arr[minIdx], arr[i]
  }
}

func main() {
  arr := []int{64, 34, 25, 12, 22, 11, 90}
  selectionSort(arr)
  fmt.Println("Sorted array is:", arr)
}
```

### Insertion Sort

```go
func insertionSort(arr []int) {
  for i := 1; i < len(arr); i++ {
    key := arr[i]
    j := i - 1

    for j >= 0 && arr[j] > key {
      arr[j+1] = arr[j]
      j--
    }
    arr[j+1] = key
  }
}

func main() {
  arr := []int{64, 34, 25, 12, 22, 11, 90}
  insertionSort(arr)
  fmt.Println("Sorted array is:", arr)
}
```

### Merge Sort

```go
func mergeSort(arr []int) []int {
  if len(arr) <= 1 {
    return arr
  }

  mid := len(arr) / 2
  left := mergeSort(arr[:mid])
  right := mergeSort(arr[mid:])

  return merge(left, right)
}

func merge(left, right []int) []int {
  result := make([]int, 0, len(left)+len(right))

  for len(left) > 0 || len(right) > 0 {
    if len(left) == 0 {
      return append(result, right...)
    }
    if len(right) == 0 {
      return append(result, left...)
    }
    if left[0] <= right[0] {
      result = append(result, left[0])
      left = left[1:]
    } else {
      result = append(result, right[0])
      right = right[1:]
    }
  }

  return result
}

func main() {
  arr := []int{64, 34, 25, 12, 22, 11, 90}
  sortedArr := mergeSort(arr)
  fmt.Println("Sorted array is:", sortedArr)
}
```

### Quick Sort

```go
func partition(arr []int, low, high int) int {
  pivot := arr[high]
  i := low - 1

  for j := low; j <= high - 1; j++ {
    if arr[j] < pivot {
      i++
      arr[i], arr[j] = arr[j], arr[i]
    }
  }
  arr[i + 1], arr[high] = arr[high], arr[i + 1]
  return i + 1
}

func quickSort(arr []int, low, high int) {
  if low < high {
    pivot := partition(arr, low, high)
    quickSort(arr, low, pivot - 1)
    quickSort(arr, pivot + 1, high)
  }
}

func main() {
  arr := []int{64, 34, 25, 12, 22, 11, 90}
  quickSort(arr, 0, len(arr) - 1)
  fmt.Println("Sorted array is:", arr)
}
```

### Heap Sort

```go
func heapify(arr []int, n, i int) {
  largest := i
  left := 2*i + 1
  right := 2*i + 2

  if left < n && arr[left] > arr[largest] {
    largest = left
  }

  if right < n && arr[right] > arr[largest] {
    largest = right
  }

  if largest != i {
    arr[i], arr[largest] = arr[largest, arr[i]
    heapify(arr, n, largest)
  }
}

func heapSort(arr []int) {
  n := len(arr)

  // Build a max heap
  for i := n/2 - 1; i >= 0; i-- {
    heapify(arr, n, i)
  }

  // Extract elements from heap on by one
  for i := n - 1; i > 0; i-- {
    arr[0], arr[i] = arr[i], arr[0]
    heapify(arr, i, 0)
  }
}

func main() {
  arr := []int{64, 34, 25, 12, 22, 11, 90}
  heapSort(arr)
  fmt.Println("Sorted array is:", arr)
}
```





# Don't Just LeetCode; Follow the Coding Patterns Instead

What if you don't like to practice 100s of coding questions before the interview?

Coding interviews are getting harder to pass. To prepare for coding interviews, you will needs weeks, if not months of preparation.

No one really likes spending that much time preparing for the coding interviews. So is there a smarter solution?

First, let's look at the problem.

Anyone preparing for coding interviews definitely knows LeetCode. It is probably the biggest online repository for coding interview questions. Lets' take a look at what problems people face when using LeetCode.

## Problems with LeetCode

There are more than 2k problems in LeetCode. The biggest challenge with LeetCode is its lack of organization; it has a huge set of coding problems, and one isn't sure where to start or what to focus on.

One wonders, is there an adequate number of questions one should go through to consider themselves prepared for the coding interviews?

I would love to see a streamlined process that guides me and teaches me enough algorithmic techniques to feel confident for the interview. As a lazy person myself, I wouldn't like to go through 500+ questions.

## The Solution

One technique that people often follow is to solve questions related to the same data structure; for example, focusing on questions related to Arrays, then LinkedList, HashMap, Heap, Tree, or Trie, etc. Although this does provide some organization , it still lacs coherence. For example, many questions can be solved using HashMaps but still require different algorithmic techniques. 

I would love to see questions set that follow not only the same data structure but also similar algorithmic techniques.

The best thing I came across was the problem-solving patterns like `Sliding Window`, `Fast and Slow Pointers,` `Topological Sort`, etc. Following these patterns helped me nurture my ability to 'map a new problem to an already known problem'. This not only made this whole coding-interview-preparation process fun but also a lot more organized.

> # Coding patterns enhance our "ability to map a new problem to an
> # already known problem."

## Coding Patterns

I have gathered around 20 of these coding problems patterns that I believer can help anyone learn these beautiful algorithmic techniques and make a real difference in the coding interviews.

The idea behind these patterns is that once you're familiar with a pattern, you'll be able to solve dozens of problems with it. For a detailed discussion of these patterns and related problems with solutions, take a look at [Grokking the Coding Interview](https://designgurus.org/course/grokking-the-coding-interview).

So, without further ado, let me list all these patterns:

1. Sliding Window
2. Islands (Matrix Traversal)
3. Two Pointers
4. Fast & Slow Pointers
5. Merge Intervals
6. Cyclic Sort
7. In-place Reversal of a LinkedList
8. Tree Breadth-First Search
9. Tree Depth First Search
10. Two Heaps
11. Subsets
12. Modified Binary Search
13. Bitwise XOR
14. Top 'K' Elements
15. K-way Merge
16. Topological Sort
17. 0/1 Knapsack
18. Fibonacci Numbers
19. Palindromic Subsequence
20. Longest Common Substring

Following is a small intro of each of these patterns with simple problems:

## 1. Sliding Window

`Usage`: This algorithmic technique is used when we need to handle the input data in a specific window size.

`DS Involved`: Array, String, HashTable

```
{[1][3][2][6][-1]}[4][1][8][2]

Then we slide the window over 1 slot

[1]{[3][2][6][-1][4]}[1][8][2]

```

`Sample Problems`:

* Longest Substring with "K" distinct Characters
* Fruits into Baskets

## 2. Islands (Matrix traversal)

`Usage`: This pattern describes all the efficient ways of traversing a matrix (or 2D array).

`DS Involved`: Matrix, Queue

```
[1][1][1][0][0]
[0][1][0][0][1]
[0][0][1][1][0]
[0][0][1][0][0]
[0][0][1][0][0]
```

`Sample Problems`:

* Number of Islands
* Flood Fill
* Cycle in a Matrix

## 3. Two Pointers

`Usage`: This technique uses two pointers to iterate input data. Generally, both pointers move in the opposite direction at a constant interval.

`DS Involved`: Array, String, LinkedList

```
Pointer 1       Pointer 2
      |           |
      V           V
     [1][2][3][4][5]
```

`Sample Problems`:

* Squaring a Sorted Array
* Dutch National Flag Problem
* Minimum Window Sort

## 4. Fast & Slow Pointers

`Usage`: Also known as Hare & Tortoise algorithm. This technique uses two pointers that traverse the input data at different speeds.

`DS Involed`: Array, String, LinkedList

```
Head         Cycle Start
 |              |
 V              V          
[1]->[2]->[3]->[4]->[5]->[6]
                ^         |
                |_________|
```

`Sample Problems`:

* Middle of the LinkedList
* Happy Number
* Cycle in a Circular Array

## 5. Merge Intervals

`Usage`: This technique is used to deal with overlapping intervals.

`DS Involved`: Array, Heap

```
-----time----->
1)[   a   ][ b ] => 'a' and 'b' do not overlap

2)[   a   ]      => 'a' & 'b' overlap, 'b' ends after 'a'
        [ b ]

3)[   a   ]      => 'a' completely overlaps 'b'
    [  b ]
    
4)  [     a   ]  => 'a' & 'b' overlap, 'a' ends after 'b'
  [  b   ]

5)  [a]          => 'b' completely overlaps 'a'
  [  b  ]

6)[  b  ][a]     => 'a' and 'b' do not overlap
```

`Sample Problems`:

* Conflicting Appointments
* Minimum Meeting Rooms

## 6. Cyclic Sort

`Usage`: Use this technique to solve array problems where the input data lies within a fixed range.

`DS Involved`: Array

`Sample Problems`:

* Find all Missing Numbers
* Find all Duplicate Numbers
* Find the First K Missing Positive Numbers

## 7. In-place Reversal of a LinkedList

`Usage`: This technique describes an efficient way to reverse the links between a set of nodes of a LinkedList. Often, the constraint is that we need to do this in-place, i.e., using the existing node objects and without using extra memory.

`DS Involved`: LinkedList

`Sample Problems`:

* Reverse every K-element Sub-list
* Rotate a LinkedList

## 8. Breadth-First Search

`Usage`: This technique is used to solve problems involving traversing trees of graphs in a breadth-first search manner.

`DS Involved`: Tree, Graph, Matrix, Queue

`Sample Problems`:

* Binary Tree Level Order Traversal
* Minimum Depth of a Binary Tree
* Connect Level Order Siblings

## 9. Depth First Search

`Usage`: This technique is used to solve problems involving traversing trees of graphs in a depth-first search manner.

`DS Involved`: Tree, Graph, Matrix

`Sample Problems`:

* Path With Given Sequence
* Count Paths for a Sum

## 10. Two Heaps

`Usage`: In many problems, we are given a set of elements that can be divided into two parts. We are interested in knowing the smallest element in one part and the biggest element in the other part. As the name suggests, this technique uses a `Min-Heap` to find the smallest element and a `Max-Heap` to find the biggest element.

`DS Involved`: Heap, Array

`Sample Problems`:

* Find the Median of a Number Stream
* Next Interval


## 11. Subsets

`Usage`: Use this technique when the problem asks to deal with permutations or combinations of a set of elements.

`DS Involved`: Queue, Array, String

`Sample Problems`:

* String Permutations by changing case
* Unique Generalized Abbreviations

## 12. Modified Binary Search

`Usage`: Use this technique to search a sorted set of elements efficiently.

`DS Involved`: Array

`Sample Problems`:

* Ceiling of a Number
* Bitonic Array Maximum

## 13. Bitwise XOR

`Usage`: This technique uses the XOR operator to manipulate bits to solve problems.

`DS Involved`: Array, Bits

`Sample Problems`:

* Two Single Numbers
* Flip and Invert an Image

## 14. Top 'K' Elements

`Usage`: This technique is used to find top/smallest/frequently occurring 'K' elements in a set.

`DS Involved`: Array, Heap, Queue

`Sample Problems`:

* 'K' Closest Points to the Origin
* Maximum Distinct Elements

## 15. K-way Merge

`Usage`: This technique helps us solve problems that involve a list of sorted arrays.

`DS Involved`: Array, Queue, Heap

`Sample Problems`:

* Kth Smallest Number in M Sorted Lists
* Kth Smallest Number in a Sorted Matrix

## 16. Topological Sort

`Usage`: Use this technique to find a linear ordering of elements that have dependencies on each other.

`DS Involved`: Array, HashTable, Queue, Graph

`Sample Problems`:

* Tasks Scheduling
* Alien Dictionary

## 17. 0/1 Knapsack

`Usage`: This technique is used to solve optimization problems. Use this technique to select elements that give maximum profit from a given set with a limitation on capacity and that each element can only be picked once.

`DS Involved`: Array, HashTable

`Sample Problems`:

* Equal Subset Sum Partition
* Minimum Subset Sum Difference

## 18. Fibonacci Numbers

`Usage`: Use this technique to solve problems that follow the Fibonacci numbers sequence, i.e., every subsequent number is calculated from the last few numbers.

`DS Involved`: Array, HashTable

`Practice Problems`:

* Staircase
* House Thief

## 19. Palindromic Subsequence

`Usage`: This technique is used to solve optimization problems related to palindromic sequences or strings.

`DS Involved`: Array, HashTable

`Sample Problems`:

* Longest Palindromic Subsequence
* Minimum Deletions in a String to make it a Palindrome

## 20. Longest Common Substring

`Usage`: Use this technique to find the optimal part of a string/sequence or set of strings/sequences.

`DS Involved`: Array, HashTable

`Sample Problems`:

* Maximum Sum Increasing Subsequence
* Edit Distance

# Conclusion

Like it of not, LeetCode-type questions are part of almost every programming interview, so every software developer should practice them before an interview. Their only option is to prepare smartly and learn problem-solving by focusing on the underlying problem patterns.

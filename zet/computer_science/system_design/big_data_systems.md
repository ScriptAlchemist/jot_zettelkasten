# Big Data Systems

* Analytics
* Machine Learning
* Data Science
* Images/Video
* Blockchain

~6 decades of research

> IBM, Microsoft, Oracle, Teradata, etc. and a gazillion start-ups
today.

* Declarative interface
* Ask "what" you want
* The system decides "how" to best store and access data
* Why is this good?

## Fundamentals of Storage

* DS
* SQL
* NoSQL
* NN
* Statistics
* Images
* Blockchains

## Data Structure

* data structure defines performance

* register: this room
* caches: this city
* memory: nearby city
* disk: Pluto

> Jim Gray Turing Award 1998


## No Perfect Structure

* Read
* Update
* Memory

There is always a give and take. Do you want better reads, more memory,
faster updates? It all depends on the specific project.

### Context that affects Design

1. Workload
2. HardWare
3. Designed performance/cloud cost

* Cache miss vs memory miss

### query x<5

Size=120
memory level N
memory level N-1

Each block of memory is 5*8 bytes

[5,10,6,4,1,2] [2,8,9,7,6] [7,11,3,9,6] ...
 x x  x o o o   0 x x x x   x x  0 x x

What are we doing here? So we are trying to solve the problem. First
finding the bottle neck. Then we need to figure out how to solve this.
How can we look at each block without having to look at each element?

What about sorting? You could sort the block.

Creating a data set and storing it and receiving the data is the
important part of this.

## Design

* Hippo Method (Highest paid person opinion matters the most)
* Standard solution and exposes knobs
  1. Design skills, application systems
  2. No one knows everything

### What if design

* layout design
* workload
* h/w

The algorithms that are already created before. You need to use them to
solve the correct problems.

> #### New & Brilliant
> I hope for nothing, I fear nothing, I am free.

Fundamental building blocks

Properties when combined

* Read
* Update
* Memory

Based on those. You need to choose the combination that works for you.
Maybe nobody has used this combination before, but you have to start
somewhere.

## Design space vs cost synthesis

1. Design concepts that does not break further

NoSQL stores had 3 fundamental structures

* B-Tree
* LSM-Tree
* Hash Tables

Depending on the kind of system you want. You will use these structures. 

## B-Tree Sorted tree

It would create boxes with a max and minimum values. Now you store
certain value. Let's say less than 15. You go to the root element and
you look at the first block. 15 isn't in that block. So it moves to the
next block. To see if the value is in between the range that you're
looking for.

What would the advantage of this be? Reads? This will be a height
optimized tree. As opposed to updates that would require moving things
around more. B+ Trees might be a better option.

> The conversation is a bit hard to follow with notes this class.

### LSM-tree

Leader since it absorbs and reads quickly

```
      Insert(key, value)
[Buffer] []                    memory
[[level 1]                              ] Disk
[[level 2       ]                       ] pages
[[level 3]                              ] SSTables
[[level n                           ]   ] leveled, tiered, sorted
```

What is Bloom filters?:

What are pence pointers?

### Hash Tables

* Key value store

The class continues on Friday as well. 

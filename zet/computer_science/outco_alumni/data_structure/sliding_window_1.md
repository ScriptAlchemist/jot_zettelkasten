# 904. Fruit Into Baskets

You are visiting a farm that has a single row of fruit trees arranged
for left to right. The trees are represented by an integer array
`fruits` where `fruits[i]` is the **type** of fruit the `i^th` tree
produces.

You want to collect as much fruit as possible. However, the owner has
some strict rules that you must follow.

* You only have **two** baskets, and each basket can only hold a **single
  type** of fruit. There is no limit on the amount of fruit each basket
  can hold.
* Starting from any tree of your choice, you much pick **exactly one
  fruit** from **every** tree (including the start tree) while moving to
  the right. The picked fruits must fit in one of your baskets.
* Once you reach a tree with fruit that cannot fit in your baskets you
  must stop.

Given the integer array `fruits`, return **maximum** number of fruits
you can pick.

### Example 1:

```
Input: fruits = [1,2,1]
Output: 3
Explanation: We can pick from all 3 trees.
```

### Example 2:

```
Input: fruits = [0,1,2,2]
Output: 3
Explanation: Wecan pick from trees [1,2,2].
If we had started at the first tree, we would only pick from trees [0,1].
```

## How can we solve this?

What do we need to do? It seems as if we have 2 baskets at most. Then we
can pick from 2 trees only. Which means that the most fruits we can get
will be from the most trees. Because we can only take 1 fruit per tree.
So we much get the most trees possible.

This makes it seem like we need to loop once to map of tree. Keeping
track of the numbers of trees available.

The brute force will be doing the two loop option.

```javascript
function fruitBasketPick(fruits) {
  const occuring = new Map();

  // Loop into map
  for (let i = 0; i < fruits.length; i++) {
    if (occuring.has(fruits[i]) {
      occuring.set(fruits[i], occuring.get(fruits[i]) + 1)
    } else {
      occuring.set(fruits[i], 1)
    }
  }

  // Find top 2 fruit options

  // Count Fruits
  for (let i = 0; i < fruits.length; i++) {

  }

}
```

Why would we loop more than once? Can we just do it once with a `Map`
and then take the top 2 options? That seems like the best option.

How can we make this a bit more performant?

What if we use some soft of priority queue. Where it will keep track of
the highest 2 numbers index's. This way it will only need to do O(n)
because it would have to go over every value in the array at least once.
We cannot do anything fancy here.

> ## These are notes while I'm in a zoom call. I don't always get all
the notes down. This is imcomplete, but it has a theoretical solution.


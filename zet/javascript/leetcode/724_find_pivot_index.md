# 724. Find Pivot Index

Given an array of integers `nums`, calculate the `pivot index` of this array.

The pivot index is the index where the sum of all the numbers strictly to the left of the index is equal to the sum of all the numbers strictly to the index's right.

If the index is on the left edge of the array, then the left sum is `0` because there are no elements to the left. This also applies to the right edge of the array. 

Return the `leftmost pivot index`. If no such index exists, return `-1`.

Example 1:

```
Input: nums = [1,7,3,6,5,6]
Output: 3
Explanation:
The pivot index is 3.
Left sum = nums[0] + nums[1] + nums[2] = 1 + 7 + 3 = 11
Right sum = nums[4] + nums[5] = 5 + 6 = 11
```

Example 2:

```
Input: nums = [1,2,3]
Output: -1
Explanation:
There is no index that satisfies the conditions in the problem
statement.
```

Example 3:

```
Input: nums = [2,1,-1]
Output: 0
Explanation:
The pivot index is 0.
Left sum = 0 (no elements to the left of index 0)
Right sum = nums[1] + nums[2] = 1 + -1 = 0
```

Constraints:

* `1 <= nums.length <= 10^4`
* `-1000 <= nums[i] <= 1000`

Sweet, now let's talk about the problem. What is it?

Let's take example 1's array 

`[1,7,3,6,5,6]`

We could first run a loop to see all the numbers in the array. 

We could also run it splitting the array in half.
* That's a stupid idea.

The way that we will accomplish this is to sum up the entire array. Then loop over the array again. O(2N) because it would loop twice. This would become O(N). Even though I don't like dropping the 2. We are talking exponential and it seems to be the consensus. It's not effecting it enough.

```
const pivotIndex = (nums) => {
  let leftSum = 0;
  let rightSum = nums.reduce((pre, cur) => pre + cur, 0);
  for(let i = 0; i < nums.length; i++) {
    rightSum -= nums[i];
    if(leftSum === rightSum) return i;
    leftSum += nums[i];
  }
  return -1;
};
```

* This uses the `.reduce()` on the full array to get the right side.
* Set the `leftSum` to 0 because we will work from the left to the right.
* Start a for loop with `i` less than `nums.length`.
* Subtract from `rightSum`.
* Compare `leftSum` with `rightSum` and if they are the same `return i`.
* If they are `NOT` the same the subtract `nums[i]` from `leftSum`.
* If it loops and never returns `i`. Then return `-1`.

Now let's find a way to make it faster.

```
const pivotIndex = (nums) => {
  let leftSum = 0;
  let rightSum = nums.reduce((pre, cur) => pre + cur, 0);
  let i = 0;
  while (i < nums.length) {
    rightSum -= nums[i];
    if (leftSum === rightSum) return i;
    leftSum += nums[i];
    i++;
  }
  return -1;
};
```

How does this improve?

* While loop to avoid the overhead of any for loops.

Another option is `for-of` loop instead of a `for` loop.

```
const pivotIndex = (nums) => {
  let leftSum = 0;
  let rightSum = nums.reduce((pre, cur) => pre + cur, 0);
  for (const num of nums) {
    rightSum -= num;
    if (leftSum === rightSum) return i;
    leftSum += num;
  }
  return -1;
};
```

But it seems like the fastest setup would be the while. `In theory`.


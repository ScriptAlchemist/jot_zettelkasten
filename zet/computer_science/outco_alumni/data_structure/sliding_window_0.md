# Sliding Window

> Fri 03/03/2023

## Sliding Window Maximum

> [LeetCode](https://leetcode.com/problems/sliding-window-maximum/description/)

You are given an array of integers `nums`, there is a sliding window of
size `k` which is moving from the very left of the array to the very
right. You can only see the `k` numbers in the windows. Each time the
sliding window moves right by one position.

Return the max sliding window.

### Example 1:

```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation:
Window position
---------------
[1  3  -1] -3  5  3  6  7      3
 1 [3  -1  -3] 5  3  6  7      3
 1  3 [-1  -3  5] 3  6  7      5
 1  3  -1 [-3  5  3] 6  7      5
 1  3  -1  -3 [5  3  6] 7      6
 1  3  -1  -3  5 [3  6  7]     7
```

### Example 2:

```
Input: nums = [1], k = 1
Output: [1]
```

### Constraints:

* `1 <= nums.length <= 10^5`
* `-10^4 <= nums[i] <= 10^4`
* `1 <= k <= nums.length`

### Basic Function Shell:

```javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
const maxSlidingWindow = function(nums, k) {

};
```

This is how the project would be setup.

### Inputs

* `nums`: Array of number
* `k`: window size

## How can we solve this?

The initial idea is just the sliding window. That is what the example is
doing.

If we break down the first example:

```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
```
The input is `nums` and `k`

`k` is telling us how big the window is. `nums` will be an array of
signed numbers.

Inside of each window you find the largest number:

```
[1  3  -1] -3  5  3  6  7      3
```

This first window has `[1, 3, -1]`. The largest number is `3`

```
 1 [3  -1  -3] 5  3  6  7      3
```

This next window brings in a `-3` and drops a `1`. The largest number is
still `3`

```
 1  3 [-1  -3  5] 3  6  7      5
```

With this next loop we bring in a `5` and lose the `3`. This presents a
different response for the max number `5`.

```
 1  3  -1 [-3  5  3] 6  7      5
```

This window brings in a `3` and drops the `-1`. The max is still `5`.

```
 1  3  -1  -3 [5  3  6] 7      6
```

This window brings in a `6` and drops the `-3`. The max changes to `6`.

```
 1  3  -1  -3  5 [3  6  7]     7
```

This window brings in a `7` and drops the `5`. The max changes to `7`.

```
Output: [3,3,5,5,6,7]
```

If we inspect the output we can see that we have `[3,3,5,5,6,7]`
returned. That is the max value in each of this windows. The window size
is `k`

## Possible Solution

We need to loop over the array of numbers in sections of `3` or the
value of `k` that is given.

```javascript
let i = 0;
const loopTill = nums.length - k;
```

We will set the `i` index value to `0`. This value changes. So it stays
a let. We also make a `loopTill` variable that is the length of `nums`
- `k`. This creates the loop parameters to loop and stop at the end of the last window.

```
while (i <= loopTill) {
  // window is nums[i], nums[i + 1], nums[i + 2]
  i++;
}
```

Now that we have the looping window what do we do now?

* Check if the window length is `window.length > nums.length`. We could
  still look inside of the window. We just need to be aware the in
  JavaScript if there is no value. It is likely undefined. Let's check
  that assumption out.

Another note. I'll be adding `1` to the `loopTill` variable so that we
can remove the `=` from the loop. We remove this to avoid 1 extra call
each time to check for `<` and `=`. Now it will just be `<` the 1 check.

```javascript
const loopTill = nums.length + 1 - k;
while (i < loopTill) {
  //...
```

We also have a problem if the `nums` array is shorter than the `k`
value. So if `nums.length >= k ? nums.length + 1 - k : 1;` This will
allow us to run 1 loop. Even if `nums.length + 1 - k` would be `0` of
less. For example, if we have `nums = [1,3]` with `k = 3`. There is a
problem with the math. `2 + 1 - 3 = 0`. Do you notice the problem? There
is a `0` value. This wouldn't allow `while (i < loopTill)` simply
because `i = 0, loopTill = 0, i === loopTill`. So instead of not
looping. We will continue to loop with the expectation that if any of
the values are undefined. That we will just ignore it.

It seems like we have the basic structure of the window we need to loop
over. That's the easy part. Now that it's been broken with with little
optimization in every decision. Let's see where the code is now:

```javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
const maxSlidingWindow = function(nums, k) {
  let i = 0;
  const loopTill = nums.length > k ? nums.length + 1 - k : 1;
  while (i < loopTill) {
    // Window access is in nums[i], nums[i + 1], nums[i + 2]
    i++;
  }
};
```

## We can peek now what?

We have the loop over the array in place. But how do we keep track of
the highest value? Do we loop over the window over and over again?

If we loop over everything over and over again. The Big O value will go
up. This means that instead of having an `O(n)` we will have a `O(n *
k)`. Which is a bit more than I would like too shoot for. I'd prefer is
just stick to `O(n)`. How can we remove the need for the extra loops? We
must keep track of it a different way.
















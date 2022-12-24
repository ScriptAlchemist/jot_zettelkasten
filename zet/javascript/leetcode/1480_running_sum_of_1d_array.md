# 1480 Running Sum of 1d Array

Given an array `nums`. We define a running sum of an array as
`runningSum[i] = sum(nums[0]...nums[i])`.

Return the running sum of `nums`.

Example 1:

```
Input: nums = [1,2,3,4]
Output: [1,3,6,10]
Explanation: Running sum is obtained as follows: [1, 1+2, 1+2+3,
1+2+3+4].
```

Example 2:

```
Input: nums = [1,1,1,1,1]
Output: [1,2,3,4,5]
```

Example 3:

```
Input: nums = [3,1,2,10,1]
Output: [3,4,6,16,17]
```

Constraints:

* `1 <= nums.length <= 1000`
* `-10^6 <= nums[i] <= 10^6`

The first solution I came up with was:

```
const runningSum = function(nums) {
    let sum = 0;
    const rArr = [];
    nums.forEach(num => {
        rArr.push(num + sum);
        sum += num;
    });
    return rArr;
};
```

Next I worked on reducing it into something smaller and faster. To avoid resizing the array over and over again we can pre-set the array length. We also use a for look because it's faster.

```
const runningSum = function(nums) {
    let sum = 0;
    const rArr = new Array(nums.length);
    for (let i = 0; i < nums.length; i++) {
        rArr[i] = nums[i] + sum;
        sum += nums[i];
    }
    return rArr;
};

```

Now we want to condense it even smaller. Hopefully to one line. The example below is incorrect. I'm currently working on making it work.

```
const runningSum = nums => nums.reduce((result, num) => {
  result.push(result[result.length - 1] + num);
  console.log(result)
  return result;
}, []);
```

Inside of the `result.push()` we will add `(result.length === 0) ? num : result[result.length - 1] + num`

This checks if the result.length is 0. If it is. It will return 0 else it will work normally. Taking the previous array value and adding to it.

```
const runningSum = nums => nums.reduce((result, num) => {
  result.push((result.length === 0) ? num : result[result.length - 1] + num);
  return result;
}, []);
```

A faster solution would be:

```
var runningSum = function(nums) {
    let n=nums.length;
    let sums=[];
    sums[0]=nums[0];

    for(let i=1;i<n;i++){
        sums.push(sums[i-1]+nums[i])
    }
    return sums;
};
```

So let's talk about it and break it down line by line.

* `let n=nums.length;`: We set a `n` variable to the input length.
* `let sums=[];`: We set an empty array.

I made an even more optimized solution:

```
const runningSum = function(nums) {
  const sums = new Array(nums.length);
  let n = nums.length;
  sums[0]=nums[0];
  for (let i = 1; i < n; i++) {
    sums[i] = sums[i-1] + nums[i];
  }
  return sums;
};
```

This just automatically sets the array length. So it won't need to be resized with a `.push()`. This way it can just loop and replace.



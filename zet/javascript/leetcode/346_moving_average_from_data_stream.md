# 346. Moving Average from Data Stream

Given a stream of integers and a window size, calculate the moving average of all integers in the sliding window

Implement the `MovingAverage` class:

* `MovingAverag(int size)` Initializes the object with the size of the window `size`
* `double next(int val)` Returns the moving average of the last `size` values of the stream


Example 1:

```
Input: ["MovingAverage", "next", "next", "next", "next"]
       [[3], [1], [10], [3], [5]]
Output: [null, 1.0, 5.5, 4.66667, 6.0]

Explaination:

MovingAverage movingAverage = new MovingAverage(3);
movingAverage.next(1); // return 1.0 = 1/1
movingAverage.next(10); // return 5.5 = (1 + 10) / 2
movingAverage.next(3); // return 4.66667 = (1 + 10 + 3) / 3
movingAverage.next(5); // return 6.0 = (10 + 3 + 5) / 3
```

```javascript
function MovingAverage(size) {
  this.window = [];
  this.maxWindow = size;

  this.next = (value) => {
    let returnVal;
    if (this.window.length >= this.maxWindow) {
      this.window.shift();
      this.window.push(value);
      returnVal = this.window.reduce((prev, cur) => prev + cur, 0) / this.window.length;
    } else {
      this.window.push(value);
      returnVal = this.window.reduce((prev, cur) => prev + cur, 0) / this.window.length;
    }
    return returnVal;
  }
} 
```

Testing the code:

```javascript
const movingAverage = new MovingAverage(3);
console.log(movingAverage.next(1)); // return 1.0 = 1/1
console.log(movingAverage.next(10)); // return 5.5 = (1 + 10) / 2
console.log(movingAverage.next(3)); // return 4.66667 = (1 + 10 + 3) / 3
console.log(movingAverage.next(5)); // return 6.0 = (10 + 3 + 5) / 3
```


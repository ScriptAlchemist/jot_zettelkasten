# Working With Date-Fns

The first example I am using the formatDistance method from date-fns. 
```javascript
import { formatDistance } from 'date-fns'
const startTime = new Date();
let t = 0;

while(t < 1_000_000) {
  t++;
  console.log(t);
  if (t === 1_000_000) {
    console.log(formatDistance(
      startTime,
      new Date(),
      { includeSeconds: true }
    ));
  }
}
```
In this solution it will create two dates. One in the beginning of the
file. Then another after t is equal to the max number provided.

* console.log makes the function take 4 minutes
* no console.log brought speeds of 3 seconds

Now for this next one we will turn the dates into milliseconds.

```javascript
import { getTime, formatDistance } from 'date-fns'
const startTime = getTime(new Date());
let t = 0;
const topNum = 1_000_000_000;

function timeDifferenceMilliseconds(start, end) {
  return end - start;
}

while(t < topNum) {
  t++;
  // console.log(t);
  if (t === topNum) {
    console.log(t);
    console.log(timeDifferenceMilliseconds(startTime, getTime(new Date())));
  }
}
```
The goal of this is to be able to see the minimum time between two dates
if possible.





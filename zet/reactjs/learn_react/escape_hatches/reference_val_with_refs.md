# Referencing Values with Refs

When you want a component to "remember" some information, but you don't
want that information to trigger new renders, you can use a ref.

> #### You will learn
> 
> * How to add a ref to your component
> * How to update a ref's value
> * How refs are different from state
> * How to use refs safely

## Adding a ref to your component

You can add a ref to your component by importing the `useRef` Hook from React:

```javascript
import { useRef } from 'react';
```

Inside our component, call the `useRef` Hook and pass the initial value that you want to reference as the only argument. For example, here is a ref to the value `0`:

```javascript
const ref = useRef(0);
```

`useRef` returns an object like this:

```javascript
{
  current: 0 // The value you passed to useRef
}
```

You can access the current value of that ref through the `ref.current` property. This value is intentionally mutable, meaning you can both read and write to it. It's like a secret pocket of your component that React doesn't track. (This is what makes it an "escape hatch" from React's one-way data flow--more on that below!)

Here, a button will increment `ref.current` on every click:

```javascript
import { useRef} from 'react';

export default funciton Counter() {
  let ref = useRef(0);

  functino handleClick=() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}
      Click me!
    </button>
  );
}
```

The ref points to a number, but like state you could point to anything: a string, and object, or even a function. Unlike state, ref is a plain JavaScript object with the `current` property that you can read and modify.

Note that the component doesn't re-render with every increment. Like state, refs are retained by React between re-renders. However, setting state re-renders a component. Changing a ref does not!

## Example: building a stopwatch

You can combine refs and state in a single component. For example, let's make a stopwatch that the user can start or stop by pressing a button. In order to display how much time has passed since the user pressed "Start", you will need to keep track of when the Start button was pressed and what the current time is. This information is used for rendering, so you'll keep it in state:

```javascript
const [startTime, setStartTime] = useState(null);
const [now, setNow] = useState(null);
```

When the user presses "Start", you'll use `setInterval` in order to update the time every 10 milliseconds:

```javascript
import { useState} from 'react';

export efault function Stopwatch() {
  const [startTime, setStartTime] = usedState(null);
  const [now, setNow] = useState(null);

  function handleStart() {
    // start counting
    setStartTime(Date.now());
    setNow(Date,now());

    setInterval(() => {
      // Update the current time every 10ms.
      setNow(Date.now());
    }, 10);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleClick}
        Start
      </button>
    </>
  );
}
```

When the "stop" button is pressed, you need to cancel the existing interval so that it stops updating the `now` state variable. You can do this by calling `clearInterval`, but you need to give it the interval ID that was previously returned by the `setInterval` call when the user pressed Start. You need to keep the interval ID somewhere. Since the interval ID is not used for rendering, you can keep it in a ref:

```javascript
import { useState, useEffect } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());



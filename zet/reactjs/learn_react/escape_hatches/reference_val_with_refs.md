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

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        Start
      </button>
      <button onClick={handleStop}>
        Stop
      </button>
    </>
  );
}
```

When a piece of information is used for rendering, keep it in state. When a piece of information is only needed by event handlers and changing it doesn't require a re-render, using a ref may be more efficient.

## Differences between refs and state

Perhaps you're thinking refs seem less "strict" than state--you can mutate them instead of always having to use a state setting function, for instance. But in most cases, you'll want to use state. Refs are an "escape hatch" you won't need often. Here's how state and refs compare:

### Refs

* `useRef(initialValue)` returns `{ current: initialValue }`
* Doesn't trigger re-render when you change it.
* `Mutable`--you can modify and update `current`'s value outside of the rendering process.
* You shouldn't read (or write) the `current` value during rendering

### state

* `useState(initialValue)` returns the current value of a state variable and a state setter function (`[value, setValue]`)
* Triggers re-render when you change it.
* `Immutable`--you must use the state setting function to modify state variables to queue a re-render.
* You can read state at any time. However, each render has its own snapshot of state which does not change.

Here is a counter button that's implemented with state:

```javascript
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  
  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You clicked {count} times
    </button>
  );
}
```

Because the `count` value is displayed, it makes sense to use a state value for it. When the counter's value is set with `setCounter()`, react re-renders the component and the screen updates to reflect the new count.

If you tried to implement this with ref, React would never re-render the component, so you'd never see the count change! See how clicking this button does not update its text:

```javascript
import { useRef } from 'react';

export default function Counter() {
  let countRef = useRef(0);

  funciton handleClick() {
    // This doesnt' re-render the component!
    countRef.current = countRef.current + 1;
  }

  return (
    <button onClick={handleClick}
      You clicked {counterRef.current} times
    </button>
  );
}
```

This is why reading `ref.current` during render leads to unreliable code. If you need that, use state instead.

> ##### Deep Dive
>
> #### How does useRef work inside?
>
> Although both `useState` and `useRef` are provided by React, in
> principle `useRef` could be implemented on top of `useState`. You can
> imagine that inside of React, `useRef` is implemented like this:

```javascript
// Inside of React
function useRef(initialValue) {
  const [ref, unused] = useState({ current: initialValue });
  return ref;
}
```

> During the first render, `useRef` returns `{ current: initialValue }`.
> This object is stored by React, so during the next render the same
> object will be returned. Note how the state setter is unused in this
> example. It is unnecessary because `useRef` always needs to return the
> same object!
>
> React provides a built-in version of `useRef` because it is common
> enough in practice. But you can think of it as a regular state
> variable without a setter. If you're familiar with object-oriented
> programming, refs might remind you of instance fields--but instead of
> `this.something` you write `somethingRef.current`.

## When to use refs

Typically, you will use a ref when your component needs to "step outside" React and communicate with external APIs--often a browser API that won't impact the appearance of the component. Here are a few of these rare situations:

* Storing timeout IDs
* Storing and manipulation DOM elements, which we cover on the next page.
* Storing other objects that aren't necessary to calculate the JSX.



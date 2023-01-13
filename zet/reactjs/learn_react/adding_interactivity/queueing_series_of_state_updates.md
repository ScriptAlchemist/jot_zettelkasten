# Queueing a Series of State Updates

Setting a state variable will queue another render. But sometimes you might want to perform multiple operations on the value before queueing the next render. To do this, it helps to understand how React batches state updates.

> #### You will learn
>
> * What "batching" is and how React uses it to process multiple state updates
> * How to apply several updates to the same state variable in a row

## React batches state updates

You might expect that clicking the "+3" button will increment the counter three times because it calls `setNumber(number + 1)` three times:

```javascript
import { useState } from 'react';

export default funciton Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  );
}
```

However, as you might recall from the previous section, each render's state values are fixed, so the value of `number` inside the first render's event handler is always `0`, no matter how many times you call `setNumber(1)`:

```
setNumber(0 + 1);
setNumber(0 + 1);
setNumber(0 + 1);
```

But there is one other factor at work here to discuss. React waits until all code in the event handlers has run before processing your state updates. This is why the re-render only happens after all these `setNumber()` calls.

This might remind you of a waiter taking an order at the restaurant. A waiter doesn't run to the kitchen at the mention of your first dish! Instead, they let you finish your order, let you make changes to it, and even take orders from other people at the table.

This lets you update multiple state variables--even from multiple components--without triggering too many re-renders. But this also means that the UI won't be updated until after your event handler, and any code in it, completes. This behavior, also known as batching, makes your React app run much faster. It also avoids dealing with confusing "half-finished" renders where only some of the variables have been updated.

React does not batch across multiple intentional events like clicks--each click is handled separately. Rest assured that React only does batching when it's generally safe to do. This ensures that, for example, if the first button click disables a form, the second click would not submit it again.

## Updating the same state variable multiple times before the next render

It is an uncommon use case, but if you would like to update the same state variable multiple times before the next render, instead of passing the next state value like `setNumber(number + 1)`, you can pass a function that calculates the next state based on the previous one in the queue, like `setNumber(n => n + 1)` It is a way to tell React to "do something with the state value" instead of just replacing it.

Try incrementing the counter now:

```javascript
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  );
}
```

Here, `n => n + 1` is called an updater function. When you pass it to a state setter:

1. React queues this function to be processed after all the other code in the event handler has run.
2. During the next render, React goes through the queue and gives you the final updated state.

```
setNumber(n => n + 1);
setNumber(n => n + 1);
setNumber(n => n + 1);
```

Here's how React works through these lines of code while executing the event handler:

1. `setNumber(n => n + 1)`: `n => n + 1` is a function. React adds it to a queue.
2. `setNumber(n => n + 1)`:`n => n + 1` is a function. React adds it to a queue.
3. `setNumber(n => n + 1)`: `n => n + 1` is a function. React adds it to a queue.

When you call `useState` during the next render, React goes through the queue. The previous `number` state was `0`, no that's what React passes to the first updater function as the `n` argument. Then React takes the return value of your previous updater function and passes it to the next updater as `n`, and so on:

| queued update |  n  |   returns   |
|    :---:      | :-: |    :--:     |
| `n => n + 1`  | `0` | `0 + 1 = 1` |
| `n => n + 1`  | `1` | `1 + 1 = 2` |
| `n => n + 1`  | `2` | `2 + 1 = 3` |

React stores `3` as the final result and returns it from `useState`.

This is why clicking "+3" in the above example correctly increments the value by 3.

## What happens if you update state after replacing it

What about this event handler? What do you think `number` will be in the next render?

```javascript
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
}}>
```

```javascript
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
      }}>Increase the number</button>
    </>
  );
}
```

Here's what this event handler tells React to do:

1. `setNumber(number + 5)`: `number` is `0`, so `setNumber(0 + 5)`. React adds "replace with `5`" to its queue.
2. `setNumber(n => n + 1)`: `n => n + 1` is an updater function. React adds that function to its queue.

During the next render, React goes through the state queue:

| queued update      |     n       |   returns  |
|      :----:        |     :-:     |   :---:    |
| "replace with `5`" |`0` (unused) |    `5`     |
| `n => n + 1`       | `5`         | `5 + 1 = 6`|

React stores `6` as the final result and returns it from `useState`.

> You may have noticed that `setState(x)` actually works like
> `setState(n => x)`, but `n` is unused!

## What happens if you replace state after updating it

Let's try one more example. What do you think `number` will be in the next render?

```javascript
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
  setNumber(42);
}}>
```

```javascript
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
        setNumber(42);
      }}>Increase the number</button>
    </>
  );
}
```

Here's how React works through these lines of code wile executing this event handler:

1. `setNumber(number + 5)`: `number` is `0`, so `setNumber(0 + 5)`. React adds "replace with `5`" to its queue
2. `setNumber(n => n + 1)`:`n => n + 1` is an updater function. React adds that function to its queue.
3. `setNumber(42)`: React adds "replace with `42`" to its queue.

During the next render, React goes through the state queue:

|   queued update    |      n      |     returns   |
|       :----:       |    :---:    |    :------:   |
| "replace with `5`" |`0` (unused) |      `5`      |
| `n => n + 1`       | `5`         |  `5 + 1 = 6`  |
| "replace with `42`"|`6` (unused) | `42`          |

Then React stores `42` as the final result and returns it from `useState`. 

To summarize, here's how you can think of what you're passing to the `setNumber` state setter:

* An updater function (e.g. `n => n + 1`) gets added to the queue
* Any other value (e.g. number `5`) adds "replace with `5`" to the queue, ignoring what's already queued.

After the event handler completes, React will trigger a re-render. During the re-render, React will process the queue. Updater functions run during rendering, so updater functions must be pure and only return the result. Don't try to set state from inside of them or run other side effects. In Strict Mode, React will run each updater function twice (but discard the second result) to help you find mistakes.

## Naming conventions

It's common to name the updater function argument by the first letters of the corresponding state variables:

```javascript
setEnabled(e => !e);
setLastName(ln => ln.reverse());
setFriendCount(fc => fc * 2);
```

If you prefer more verbose code, another common convention is to repeat the full state variable name, like `setEnabled(enabled => !enabled)`, or to use a prefix like `setEnabled(prevEnabled => !prevEnabled)`.

## Recap

* Setting state does not change the variable in the existing render, but it requests a new render.
* React processes state updates after event handlers have finished running. This is called batching.
* To update some state multiple times in one event, you can use `setNumber(n => n + 1)` updater function.

# Challenge

## Challenge 1 of 2: Fix a request counter

You're working on an art marketplace app that lets the user submit multiple orders for an art item at the same time. Each time the user presses the "Buy" buttons, the "Pending" counter should increase by one. After three seconds, the "Pending" counter should decrease, and the "Completed" counter should increase.

However, the "Pending" counter does not behave as intended. When you press "Buy", it decreases to `-1` (which should not be possible!). And if you click fast twice, both counters seem to behave unpredictably.

Why does this happen? Fix both counters.

```javascript
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(pending + 1);
    await delay(3000);
    setPending(pending - 1);
    setCompleted(completed + 1);
  }

  return (
    <>
      <h3>
        Pending: {pending}
      </h3>
      <h3>
        Completed: {completed}
      </h3>
      <button onClick={handleClick}>
        Buy
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

## Challenge 2 of 2: Implement the state queue yourself

In this challenge, you will reimplement a tiny part of React from scratch! It's not as hard as it sounds.

Scroll through the sandbox preview. Notice that it shows four test cases. They correspond to the examples you've seen earlier on this page. Your task is to implement the `getFinalState` function so that it returns the correct result for each of those cases. If you implement it correctly, all four tests should pass.

You will receive two arguments: `baseState` is the initial state (like `0`), and the `queue` is an array which contains a mix of numbers (like `5`) and updater functions (like `n => n + 1`) in the order they were added.

Your task is to return the final state, just like the tables on this page show!

`App.js`:

```javascript
import { getFinalState } from './processQueue.js';

function increment(n) {
  return n + 1;
}
increment.toString = () => 'n => n+1';

export default function App() {
  return (
    <>
      <TestCase
        baseState={0}
        queue={[1, 1, 1]}
        expected={1}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          increment,
          increment,
          increment
        ]}
        expected={3}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
        ]}
        expected={6}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
          42,
        ]}
        expected={42}
      />
    </>
  );
}

function TestCase({
  baseState,
  queue,
  expected
}) {
  const actual = getFinalState(baseState, queue);
  return (
    <>
      <p>Base state: <b>{baseState}</b></p>
      <p>Queue: <b>[{queue.join(', ')}]</b></p>
      <p>Expected result: <b>{expected}</b></p>
      <p style={{
        color: actual === expected ?
          'green' :
          'red'
      }}>
        Your result: <b>{actual}</b>
        {' '}
        ({actual === expected ?
          'correct' :
          'wrong'
        })
      </p>
    </>
  );
}
```

`processQueue.js`:

```javascript
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  // TODO: do something with the queue...

  return finalState;
}
```

# Solutions

## Challenge 1 of 2

Inside the `handleClick` event handler, the values for `pending` and `completed` correspond to what they were at the time of the click event. For the first render, `pending` was `0`, so `setPending(pending - 1)` becomes `setPending(-1)`, which is wrong. Since you want to increment or decrement the counters, rather than set them to a concrete value determined during the click, you can instead pass the updater functions:

```javascript
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(p => p + 1);
    await delay(3000);
    setPending(p => p - 1);
    setCompleted(c => c + 1);
  }

  return (
    <>
      <h3>
        Pending: {pending}
      </h3>
      <h3>
        Completed: {completed}
      </h3>
      <button onClick={handleClick}>
        Buy     
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

This ensures that when you increment or decrement a counter, you do it in relation to its latest state rather than what the state was at the time of the click.

## Challenge 2 of 2

This is the exact algorithm described on this page that React uses to calculate the final state:

```javascript
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  for (let update of queue) {
    if (typeof update === 'function') {
      finalState = update(finalState)
    } else {
      finalState = update;
    }
  }

  return finalState;
}
```

```
Base state: 0

Queue: [1, 1, 1]

Expected result: 1

Your result: 1 (correct)

Base state: 0

Queue: [n => n+1, n => n+1, n => n+1]

Expected result: 3

Your result: 3 (correct)

Base state: 0

Queue: [5, n => n+1]

Expected result: 6

Your result: 6 (correct)

Base state: 0

Queue: [5, n => n+1, 42]

Expected result: 42

Your result: 42 (correct)
```

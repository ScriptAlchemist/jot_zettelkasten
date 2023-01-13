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



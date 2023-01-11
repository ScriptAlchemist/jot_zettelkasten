# Responding to Events

React lets you add event handlers to your JSX. Event handlers are your
own functions that will be triggered in response to interaction like
clicking, hovering, focusing form inputs, and so on.

> ### You will learn
> 
> * Different ways to write an event handler
> * How to pass event handling logic from a parent component
> * How events propagate and how to stop them

## Adding event handlers

To add an event handler, you will first define a function and then pass
it as a prop to the appropriate JSX tag. For example, here is a button
that doesn't do anything yet:

```javascript
export default function Button() {
  return (
    <button>
      I don't do anything
    </button>
  );
}
```

You can make it show a message when a user clicks by following these three steps:

1. Declare a function called `handleClick` inside you `Button` component.
2. Implement the logic inside that function (use `alert` to show the message).
3. Add `onClick={handleClick}` to the `<button>` JSX.

```javascript
export default function Button() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```

You defined the `handleClick` function and then `passed it as a prop` to `<button>`. `handleClick` is and event handler. Event handler functions:

* Are usually defined inside you components.
* Have names that start with `handle`, followed by the name of the event.

> By convention, it is common to name event handler as `handle` followed
> by the event name. You'll often see `onClick={handleClick}`,
> `onMouseEnter={handleMouseEnter}`, and so on.

Alternatively, you can define an event handler inline in the JSX:

```javascript
<button onClick={function handleClick() {
  alert('You clicked me!');
}}>
```

Or, more concisely, using an arrow function:

```javascript
<button onClick={() => {
  alert('You clicked me!');
}}>
```

All of these styles are equivalent. Inline event handlers are convenient for short functions.

> ### Pitfall
>
> Functions passed to event handlers must be passed, not called. For
> example:
>
> | passing a function (correct)   | calling a function (incorrect) |
> |          :----:                |            :----:              |
> |`<button onClick={handleClick}>`| `<button onClick={handleClick()}>`|
>
> The difference is subtle. In the first example, the `handleClick`
> function is passed as an `onClick` event handler. This tells React to
> remember it an only call your function when the user click to button.
>
> In the second example, the `()` at the end of `handleClick()` fines
> the function *immediately* during `rendering`, without any clicks.
> This is because JavaScript inside of `JSX` `{` and `}` executes right
> away.
>
> When you write code inline, the same pitfall presents itself in
> a different way:
>
> | passing a function (correct) | calling a function (incorrect) |
> |         :-----:              |            :-----:             |
> |`<button onClick={() => alert('...')}>` |`<button
> onClick={alert('...')}>` |

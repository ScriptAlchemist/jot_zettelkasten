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
> |`<button onClick={() => alert('...')}>` |`<button> onClick={alert('...')}>` |
>
> Passing inline code like this won't fie on click--it fires every time
> the component renders:

```
// This alert fires when the componnet render, not when clicked
<button onClick={alert('You clicked me!')}>
```

> If you want to define your event handler inline, wrap it in an anonymous function like so:

```
<button onClick={() => alert('You clicked me!')}>
```

> Rather than executing the code inside with every render, this create a function to be called later.
>
> In both cases, what you want to pass is a function:
> * `<button onClick={handleClick}>` passes the `handleClick` function.
> * `<button onClick={() => alert('...')}>` passes the `() => alert('...')` function.

## Reading props in event handlers

Because event handlers are declared inside of a component, they have access to the component's props. Here is a button that, when clicked, shows an alert with its `message` prop:

```javascript
function AlertButton({ message, children }) {
  return (
    <button onClick={() => alert(message)}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <AlertButton message="Playing!">
        Play Movie
      </AlertButton>
      <AlertButton message="Uploading!">
        Upload Image
      </AlertButton>
    </div>
  );
}
```

This lets these two buttons show different messages. Try changing the messages passed to them.

## Passing event handlers as props

Often you'll want the parent component to specify a child's event handler. Consider buttons: depending on where you're using a `Button` component, you might want to execute a different function--perhaps one plays a movie and another uploads an image.

To do this, pass a prop the component receives form its parent as the event handler like so:

```javascript
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

function PlayButton({ movieName }) {
  function handlePayClick() {
    alert(`Playing ${movieName}!`);
  }

  return (
    <Button onClick={handlePlayClick}>
      Play "{movieName}"
    </Button>
  );
}

function UploadButton() {
  return (
    <Button onClick={() => alert('Uploading!')}>
      Upload Image
    </Button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <PlayButton movieName="Kiki's Devlivery Service" />
      <UploadButton />
    </div>
  );
}
```

Here, the `Toolbar` component renders a `PlayButton` and an `UploadButton`:

* `PlayButton` passes `handlePlayClick` as the `onClick` prop to the `Button` inside
* `UploadButton` passes `() => alert('Uploading!')` as the `onClick` prop to the `Button` inside.

Finally, you `Button` component accepts a prop called `onClick`. it passes that prop directly to the built-in browser `<button>` with `onClick={onClick}`. This tells React to call the passed function on click.

If you use a design system, it's common for components like buttons to contain styling but not specify behavior. Instead, components like `PlayButton` and `uploadButton` will pass event handlers down.

## Naming event handler props

Built-in components like `<buttons>` and `<div>` only support browser evert names like `onClick`. However, when you're building your own components, you can name their event handler props any way that you like.

> By convention, event handler props should start with `on`, followed by
> a capital letter.

For example, the `Button` component's `onClick` prop could have been called `onSmash`:

```javascript
function Button({ onSmash, children }) {
  return (
    <button onClick={onSmash}>
      {children}
    </button>
  );
}

export default function App() {
  return (
    <div>
      <Button onSmash={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onSmash={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```

In this example, `<button onClick={onSmash}>` shows that the browser `<button>` (lowercase) still needs a prop called `onClick`, but the prop name received by your custom `Button` component is up to you!

When your component supports multiple interactions, you might name event handler props for app-specific concepts. For example, this `Toolbar` component receives `onPlayMovie` and `onUploadImage` event handlers:

```javascript
exort default function App() {
  return (
    <Toolbar
      onPlayMovie={() => alert('Playing!')}
      onUploadImage={() => alert('Uploading!')]
    />
  );
}

function Toolbar({ onPlayMovie, onUploadImage }) {
  return (
    <div>
      <Button onClick={onPlayMovie}>
        Play Movie
      </Button>
      <Button onClick={onUploadImage}>
        Upload Image
      </Button>
    </div>
  );
}

function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```




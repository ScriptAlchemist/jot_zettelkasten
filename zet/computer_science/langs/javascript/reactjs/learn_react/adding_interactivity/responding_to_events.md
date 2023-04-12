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

Notice how the `App` component does not need to know what `Toolbar` will do with `onPlayMovie` or `onUploadImage`. That's an implementation detail of the `Toolbar`. Here, `Toolbar` passes them down as `onClick` handlers to its `Button`s, but it could later also trigger them on a keyboard shortcut. Naming props after app-specific interactions like `onPlayMovie` gives you the flexibility to change how they're used later.

## Event propagation

Event handlers will also catch events from any children your component might have. We say that an event "bubble" or "propagates" up the tree: it starts with where the event happened, and then goes up the tree.

This `<div>` contains two buttons. Both the `<div>` and each button have their own `onClick` handlers. Which handlers do you think will fire when you click a button?

```javascript
export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!');
    }}>
      <button onClick={() => alert('Playing!')}>
        Play Movie
      </button>
      <button onClick={() => alert('Uploading!')}>
        Upload Image
      </button>
    </div>
  );
}
```

f you click on either button, its `onClick` will run first, followed by the parent `<div>`'s `onClick`. So two messages will appear. If you click the toolbar itself, only the parent `<div>`'s `onClick` will run.

> #### Pitfall
>
> All events propagate in React except `onScroll`, which only works on
> the JSX tag you attach it to.

## Stopping propagation

Event handlers receive an event object as their only argument. By convention, it's usually called `e`, which stands for "event". You can use this object to read information about the event.

That event object also lets you stop the propagation. If you want to prevent an event from reaching parent components, you need to call `e.stopPropagation()` like this `Button` component does:

```javascript
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!);
    }}>
      <Button onClick={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onClick={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```

When you click on a button:

1. React calls the `onClick` handler passed to `<button>`.
2. That handler, defined in `Button`, does the following:
  * Calls `e.stopPropagation()`, preventing the event from bubbling further.
  * Calls for `onClick` function, which is a prop passed form the `Toolbar` component
3. That function, defined in the `Toolbar` component, displays the button's own alert
4. Since the propagation was stopped, the parent `<div>`'s `onClick` handler does not run

As a result of `e.stopPropagation()`, clicking on the buttons now only shows a single alert (from the `<button>`) rather than the two of them (from the `<button>` and the parent toolbar `<div>`). Clicking a button is not the same thing as clocking the surrounding toolbar, so stopping the propagation makes sense for this UI

> ##### Deep Dive
>
> #### Capture phase events
>
> In the rare cases, you might need to catch all events on child
> elements, even if they stopped propagation. For example, maybe you
> want to log every click to analytics, regardless of the propagation
> logic. You can do this by adding `Capture` at the end of the event
> name:

```
<div onClickCapture={() => { /* this runs first */ }}>
  <button onClick={e => e.stopPropagation()} />
  <button onClick={e => e.stopPropagation()} />
</div>
```

> Each event propagates in three phases:
>
> 1. It travels down, calling all `onClickCapture` handlers.
> 2. It runs the clicked element's `onClick` handler.
> 3. It travels upwards, calling all `onClick` handlers.
>
> Capture events are useful for code like routers or analytics, but you
> probably won't use them in app code.

## Passing handlers as alternative to propagation

Notice how this click handler runs a line of code and then calls the `onClick` prop passed by the parent:

```javascript
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}
```

You could add more code to this handler before calling the parent `onClick` event handler, too. This pattern provides an alternative to propagation. It lets the child component handle the event, while also letting the parent component specify some additional behavior. Unlike propagation, it's not automatic. But the benefit of this pattern is that you can clearly follow the whole chain code that executes as a result of some event.

If you rely on propagation and it's difficult to trace which handlers execute an why, try this approach instead.

## Preventing default behavior

Some browser events have default behavior associated with them. For example, a `<form>` submit event, which happens when a button inside of it is clicked, will reload the whole page by default:

```javascript
export defulat function Signup() {
  return (
    <form onSubmit={() => alert('Submitting!')}>
      <input />
      <button>Send</button>
    </form>
  );
}
```

You can call `e.preventDefault()` on the event object to stop this from happening:

```javascript
export default function Signup() {
  return (
    <form onSubmit={e => {
      e.preventDefault();
      alert('Submitting!');
    }}>
      <input />
      <button>Send</button>
    </form>
  );
}
```

Don't confuse `e.stopPropagation()` and `e.preventDefault()`. They are both useful, but are unrelated:

* `e.stopPropagation()` stops the event handlers attached to the tags above from firing.
* `e.preventDefault()` prevents the default browser behavior for a few events that have it.

## Can event handlers have side effects?

Absolutely! Event handlers are the best place for side effects.

Unlike rendering functions, event handlers don't need to be pure, so it's a great place to change something--for example, change an input's value in response to typing, or change a list
in response to a button press. However, in order to change some information, you first need some way to store it. In react, this is done by using `state, a component's memory`. You will learn all about it on the next page.

## Recap

* You can handle events by passing a function as a prop to and element like `<button>`
* Event handlers must be passed, not called! `onClick={handleClick}`, not `onClick={handleClick()}`.
* You can define an event handler function separately or inline.
* Event handlers are defined inside a component, so they can access props.
* You can declare an event handler in a parent and pass it as a prop to a child.
* You can define you own event handler props with application-specific names.
* Events propagate upwards. Call `e.stopPropagation()` on the first argument to prevent that.
* Events may have unwanted default browser behavior. Call `e.preventDefault()` to prevent that.
* Explicitly called an event handler prop from a child handler is a good alternative to propagation.

# Challenges

## Challenge 1 of 2: Fix an event handler

Clicking this button is supposed to switch the page background between white and black. However, nothing happens when you click it. Fix the problem. (Don't worry about the logic inside `handleClick`--that part is fine.)

```javascript
export default function LightSwitch() {
  function handleClick() {
    let bodyStyle = document.body.style;
    if (bodyStyle.backgroundColor === 'black') {
      bodyStyle.backgroundColor = 'white';
    } else {
      bodyStyle.backgroundColor = 'black';
    }
  }

  return (
    <button onClick={handleClick()}>
      Toggle the lights
    </button>
  );
}
```

## Challenge 2 of 2: Wire up the events

This `ColorSwitch` component renders a button. It's supposed to change the page color. Wire it up to the `onChangeColor` event handler prop it receives from the parent so that clicking the button changes the color.

After you do this, notice that clicking the button also increments the page click counter. Your colleague who wrote the parent component insist that `onChangeColor` does not increment any counters. What else might be happening? Fix it so that clicking the button only changes the color, and does not increment the counter.

```javascript
export default function ColorSwitch({
  onChangeColor
}) {
  return (
    <button>
      Change color
    </button>
  );
}
```

# Solutions

## Challenge 1 of 2

The problem is that `<button onClick={handleClick()}>` calls the `handleClick` function while rendering instead of passing it. Removing the `()` call so that it's `<button onClick={handleClick}>` fixes the issue:

```javascript
export default function LightSwitch() {
  function handleClick() {
    let bodyStyle = document.body.style;
    if (bodyStyle.backgroundColor === 'black') {
      bodyStyle.backgroundColor = 'white';
    } else {
      bodyStyle.backgroundColor = 'black';
    }
  }

  return (
    <button onClick={handleClick}>
      Toggle the lights
    </button>
  );
}
```

Alternatively, you could wrap the call into another function, like `<button onClick={() => handleClick()}>`:

```javascript
export default function LightSwitch() {
  function handleClick() {
    let bodyStyle = document.body.style;
    if (bodyStyle.backgroundColor === 'black') {
      bodyStyle.backgroundColor = 'white';
    } else {
      bodyStyle.backgroundColor = 'black';
    }
  }

  return (
    <button onClick={() => handleClick()}>
      Toggle the lights
    </button>
  );
}
```

## Challenge 2 of 2

First, you need to add the event handler, like `<button onClick={onChangeColor}>`.

However, this introduces the problem of the incrementing counter. If `onchangeColor` does not do this, as your colleague insists, then the problem is that this event propagates up, and some handler above does it. To solve this problem, you need to stop the propagation. But don't forget that you should still call `onChangeColor`.

```javascript
export default function ColorSwitch({
  onChangeColor
}) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onChangeColor();
    }}>
      Change color
    </button>
  );
}
```



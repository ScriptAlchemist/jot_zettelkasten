# Synchronizing with Effects

Some components need to synchronize with external systems. For example, you might want to control a non-React component based on the React state, set up a server connection, or send an analytics log when a component appears on the screen. Effects lets you run some code after rendering so that you can synchronize your component with some system outside of React.

> #### You will learn
>
> * What Effects are
> * How Effects are different from events
> * How to declare an Event in your component
> * How to skip re-running an Effect unnecessarily
> * Why Effects run twice in development and how to fix them

## What are Effect and how are they different form events?

Before getting to Effects, you need to be familiar with two types of logic inside React components:

* `Rendering code` (introduced in Describing the UI) lives at the top level of your component. This is where you take the props and state, transform them, and return the JSX you want to see on the screen. Rendering code must be pure. Like a math formula, it should only calculate the result, but not do anything else.
* `Event handlers` (introduced in Adding Interactivity) are nested functions inside your components that do things rather than just calculate them. An event handler might update an input field, submit an HTTP POST request to buy a product, or navigate the user to another screen. Event handlers contain "side effects" (they change the program's state) and are caused by a specific user action (for example, a button click or typing).

Sometimes this isn't enough. Consider a `ChatRoom` component that must connect to the chat server whenever it's visible on the screen. Connecting to a server is not a pure calculation (it's a side effect) so it can't happen during rendering. However, there is no single particular event like a click that causes `ChatRoom` to be displayed.

Effects let you specify side effect that are caused by rendering itself,
rather than by a particular event. Sending a message in the chat is an
event because it is directly caused by the user clicking a specific
button. However, setting up a server connection is an Effect because it
needs to happen regardless of which interaction caused the component to
appear. Effect run at the end of the rendering process after the screen
updates. This is a good time to synchronize the React component with some external system (like network or a third-party library).

> #### Note
>
> Here and later in this text, capitalize "Effect" refers to the
> React-specific definition above, i.e. a side effect caused by
> rendering. To refer to the broader programming concept, we'll say
> "side effect".

## You might not need an Effect

Don't rush to add Effects to your components. Keep in mind that Effects are typically used to "step out" of your React code and synchronize with some external system. This includes browser APIs, third-party widgets, network, and so on. If your Effect only adjust some state based on other state, you might not need an Effect.

## How to write an Effect

To write an Effect, follow these three steps:

1. Declare and Effect. By default, your Effect will run after every render.
2. Specify the Effect dependencies. Most Effects should only re-run when needed rather than after every render. For example, a fade-in animation should only trigger when a component appears. Connecting and disconnection to a chat room should only happen when the component appears and disappears, or when the chat room changes. You will learn how to control this by specifying dependencies.
3. Add cleanup if needed. Some Effects need to specify how to stop, undo, or clean up whatever they were doing. For example, "connect" needs "disconnect", "subscribe" needs "unsubscribe", and "fetch" needs either "cancel" or "ignore". You will learn how to do this by returning a cleanup function.

Let's look at each of these steps in detail.

## Step 1: Declare an Effect

To declare an Effect in your component, import the `useEffect` Hook from React:

```javascript
import { useEffect } from 'react';
```

Then, call it at the top level of your component and put some code inside your Effect:

```javascript
function MyComponent() {
  useEffect(() => {
    // code here will run after *every* render
  });
  return <div />
}
```

Every time your component renders, React will update the screen and then run the code inside `useEffect`. In other words, `useEffect` "delays" a piece of code from running until that render is reflected on the screen.

Let's see how you can use an Effect to synchronize with an external system. Consider a `<VideoPlayer>` React component. It would be nice to control whether it's playing or paused by passing an `isPlaying` prop to it:

```javascript
<VideoPlayer isPlaying={isPlaying} />;
```

Your custom `VideoPlayer` component renders the built-in browser `<video>` tag:

```javascript
function VideoPlayer({ src, isPlaying }) {
  // TODO: do something with isPlaying
  return <video src={src} />;
}
```

However, the browser `<video>` tag does not have an `isPlaying` prop. The only way to control it is to manually call the `play()` and `pause()` methods on the DOM element. You need to synchronize the value of `isPlaying` prop, which tells whether the video should currently be playing, with imperative calls like `play()` and `pause()`.

We'll need to first get a ref to the `<video>` DOM node.

You might be tempted to try to call `play()` or `pause()` during rendering, but that isn't correct:

```javascript
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  if (isPlaying) {
    ref.current.play(); // Calling these while rendering isn't allowed
  } else {
    ref.current.pause(); // Also, this crashes
  }

  return <video ref={ref} src={src} loop playsInline />
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interctive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

The reason this code isn't correct is that it tries to do something with the DOM node during rendering. In React, rendering should be a pure calculation of JSX and should not contain side effects like modifying the DOM.

Moreover, when `VideoPlayer` is called for the first time, its DOM does not exist yet! There isn't a DOM node yet to call `play()` or `pause()` on, because React doesn't know what DOM to create until after you return the JSX.

The solution here is to wrap the side effect with `useEffect` to move it out of the rendering calculation:

```javascript
import { useEffect, useRef } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });
  
  return <video ref={ref} src={src} loop playsInline />;
}
```

By wrapping the DOM update in an Effect, you let React update the screen first. then your Effect runs.

When your `VideoPlayer` component renders (either the first time or if it re-renders), a few things will happen. First, React will update the screen, ensuring the `<video>` tag is in the DOM with the right props. Then React will run your Effect. Finally, your Effect will call `play()` or `pause()` depending on the value of `isPlaying` prop.

Press Play/Pause multiple times and see how the video player stays synchronize to the `isPlaying` value:

```javascript
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}

expoer default function App() {
  const [isPlaying, setIsPlaying] = useState(false);

  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

In this example, the "external system" you synchronized to React state was the browser media API. You can use a similar approach to wrap legacy non-React code (like JQuery plugins) into declarative React components.

Note that controlling a video player is much more complex in practice. Calling `play()` may fail, the user might play or pause using the built-in browser control, and so on. This example, is very simplified and incomplete.

> #### Pitfall
>
> By default, Effects run after every render. This is why code like this
> will produce an infinite loop:

```javascript
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(count + 1);
});
```

> Effects run as a result of rendering. Setting state triggers
> rendering. Setting state immediately in an Effect is like plugging
> a power outlet into itself. The Effect runs, it sets the state, which
> causes a re-render, which causes the Effect to run, it sets the state
> again, this causes another re-render, and so on.
>
> Effects should usually synchronize your components with an external
> system. If there's no external system an you only want to adjust some
> state based on other state, you might not need an Effect

## Step 2: Specify the Effect dependencies

By default, Effects run after every render. Often, this is not what you want:

* Sometimes, it's slow. Synchronizing with an external system is not always instant, so you might want to skip doing it unless it's necessary. For example, you don't want to reconnect to the chat server on every keystroke.
* Sometimes, it's wrong. For example, you don't want to trigger a component fade-in animation on every keystroke. The animation should only play once when the component appears for the first time.

To demonstrate the issue, here is the previous example, with a few `console.log` calls and a text input that updates the parent component's state. Notice how typing causes the Effect to re-run:

```javascript
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('Calling video.play()');
      ref.current.play();
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onchange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```


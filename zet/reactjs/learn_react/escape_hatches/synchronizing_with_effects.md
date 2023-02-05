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

You can tell React to skip unnecessarily re-running the Effect by specifying an array of dependencies as the second argument to the `useEffect` call. Start by adding and empty `[]` array to the above example on line 14:

```javascript
useEffect(() => {
  // ...
}, []);
```

You should see an error saying `React Hook useEffect has a missing dependency: 'isPlaying`:

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
  }, []); // This causes an error

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
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

The problem is that the code inside of your Effect depends on the `isPlaying` prop to decide what to do, but this dependency was not explicitly declared. To fix this issue, add `isPlaying` to the dependency array:

```javascript
useEffect(() => {
  if (isPlaying) {
    // ...
  } else {
    // ...
  }
}, [isPlaying]); // ...so it must be declared here!
```

Now all dependencies are declared, so there is no error. Specifying `[isPlaying]` as the dependency array tells React that it should skip re-running your Effect if `isPlaying` is the same is it was during the previous render. With this change, typing into the input doesn't cause the Effect to re-run, but pressing Play/Pause does:

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
  }, [isPlaying]);

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={ e => setText(e.target.value)} />
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

The dependency array can contain multiple dependencies. React will only skip re-running the Effect if all of the dependencies you specify have exactly the same values as they had during the previous render. React compares the dependency values using the `Object.is` comparison. See the `useEffect` API reference for more details.

Notice that you can't "choose" your dependencies. You will get a lint error if the dependencies you specified don't match what React expects based on the code inside your Effect. This helps catch many bugs in your code. If your Effect uses some value but you don't want to re-run the Effect when it changes, you'll need to edit the Effect code itself to not "need" that dependency.

> #### Pitfall
>
> The behaviors without the dependency array and with an empty `[]`
> dependency array are very different:

```javascript
useEffect(() => {
  // This runs afer every render
});

useEffect(() => {
  // This runs only on mount (when the component appears)
}, []);

useEffect(() => {
  // This runs on mount *and also* if either a or b have changed since the last render
}, [a, b]);
```

> We'll take a close look at what "mount" means in the next step.

---

> ##### Deep Dive
> #### Why was the ref omitted from the dependency array?
>
> This Effect uses both `ref` and `isPlaying`, but only `isPlaying` is
> declared as a dependency:

```javascript
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying]);
```

> This is because the `ref` object has a stable identity: React
> guarantees you'll always get the same object from the same `useRef`
> call on every render. It never changes, so it will never by itself
> cause the Effect to re-run. Therefore, it does not matter whether you
> include it of not. Including it is fine too:

```javascript
function VideoPlaye({ src, isPlaying }) {
  const ref = useRef(null);
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying, ref]);
```

> The `set` functions returned by `useState` also have stable identity,
> so you will often see them omitted from the dependencies too. If the
> linter lets you omit a dependency without errors, it is safe to do.
>
> Omitting always-stable dependencies only works when the linter can
> "see" that the object is stable. For example, if `ref` was passed from
> a parent component, you would have to specify it in the dependency
> array. However, this is good because you can't know whether the parent
> component always passes the same ref. or passes one of several refs
> conditionally. So your Effect would depend on which ref is passed.

## Step 3: Add cleanup if needed

Consider a different example. You're writing a `ChatRoom` component that needs to connect to the chat server when it appears. You are given a `createConnection()` APO that returns an object with `connect()` and `disconnect()` methods. How do you keep the component connected while it is displayed to the user?

Start by writing the Effect logic:

```javascript
useEffect(() => {
  const connection = createConnection();
  connection.connect();
});
```

It would be slow to connect to the chat after every re-render, so you add the dependency array:

```javascript
useEffect(() => {
  const connection = createConenction();
  connection.connect(0;
}, []);
```

The code inside the Effect does not use any props or state, so your dependency array is `[]` (empty). This tells React to only run this code when the component "mounts", i.e. appears on the screen for the first time.

Let's try running this code:

`App.js`:

```javascript
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

`chat.js`:

```javascript
export function createConnection() {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting...');
    },
    disconnect() {
      console.log('❌ Disconnected.');
    }
  };
}
```

This Effect only runs on mount, so you might expect "✅ Connecting..." to be printed once in the console. However, if you check the console, "✅ Connecting..." get printed twice. Why does it happen?

Imagine the `ChatRoom` component is a part of a larger app with many different screens. The user starts their journey on the `ChatRoom` page. The component mounts and calls `connection.connect()`. Then imagine the user navigates to another screen--for example, to the Settings page. The `ChatRoom` component unmounts. Finally, the user clicks Back and `ChatRoom` mounts again. This would set up a second connection--but the first connection was never destroyed! As the user navigates across the app, the connections would keep piling up.

Bugs like this are easy to miss without extensive manual testing. To help you spot them quickly, in development React remounts every component once immediately after its initial mount. Seeing the "✅ Connecting..." log twice helps you notice the real issue: your code doesn't close the connection when the component unmounts.

To fix the issue, return a cleanup function form your Effect:

```javascript
useEffect(() = > {
  const connection = createConnection();
  connection.connect();
  return () => {
    connection.disconnect();
  };
}, []);
```

React will call your cleanup function each time before the Effect runs again, and on final time when the component unmounts (gets removed). Let's see what happens when the cleanup function is implemented:

```javascript
import { useState, useEffect } from 'react';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

`chat.js`:

```javascript
export function createConnection() {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting...');
    },
    disconnect() {
      console.log('❌ Disconnected.');
    }
  };
}
```

Now you get three console logs in development:

1. "✅ Connection..."
2. "❌ Disconnected."
3. "✅ Connecting..."

This is the correct behavior in development. By remounting your component, React verifies that navigating away and back would not break you code. Disconnecting and the connecting again is exactly what should happen! When you implement the cleanup well, there should be no user-visible difference between running the Effect once vs running it, cleaning it up, and running it again. There's an extra connect/disconnect call pair because React is probing your code for bugs in development. This is normal and you shouldn't try to make it go away.

In production, you would only see "✅ Connecting..." printed once. Remounting components only happens in development to help you find Effect that need cleanup. You can turn off `Strict Mode` to opt out of the development behavior, but we recommend keeping it on. This lets you find many bugs like the one above.

## How to handle the Effect firing twice in development?

React intentionally remounts your component in development to help you find bugs like in the last example. The right question isn't "how to run an Effect once", but "how to fix my Effect so that it works after remounting".

Usually, the answer is to implement the cleanup function. The cleanup function should stop or undo whatever the Effect was doing. The rule of thumb is that the user shouldn't be able to distinguish between the Effect running once (as in production) and a setup -> cleanup -> setup sequence (as you'd see in development).

Most of the Effects you'll write will fit into one of the common patterns below.

### Controlling non-React widgets

Sometimes you need to add UI widgets that aren't written in React. For example, let's say you're adding a map component to your page. It has a `setZoomLevel()` method, and you'd like to keep the zoom level in sync with `zoomLevel` state variable in your React code. Your Effect would look like similar to this:

```javascript
useEffect(() => {
  const map = mapRef.current;
  map.setZoomLevel(zoomLevel);
}, [zoomLevel]);
```

Note that there is no cleanup needed in this case. In development, React will call the Effect twice, but this is not a problem because calling `setZoomLevel` twice with the same value does not do anything. It may be slightly slower, but this doesn't matter because the remounting is development-only and won't happen in production.

Some APIs may not allow you to call them twice in a row. For example, the `showModal` method on the built-in `<dialog>` element throws if you call it twice. Implement the cleanup function and make it close the dialog:

```javascript
useEffect(() => {
  const dislog = dialogRef.current;
  dialog.showModal();
  return () => dialog.close();
}, []);
```

In development, your Effect will call `showModal()`, then immediately `close()`, and then `showModal()` again. This has the same user-visible behavior as calling `showModal()` once, as you would see in production.

### Subscribing to events

If you Effect subscribes to something, the cleanup function should unsubscribe:

```javascript
useEffect(() => {
  function handleScroll(e) {
    console.log(e.clientX, e.clientY);
  }
  window.addEventListener('scroll', handleScroll);
  return () => window.removeEventListener('scroll', handleScroll);
}, []);
```

In development, your Effect will call `addEventListener()`, then immediately `removeEventListener()`, and then `addEventListener()` again with the same handler. So there would be only one active subscription at a time. This has the same user-visible behavior as calling `addEventListener()` once, as you would see in production.

### Triggering animations

If your Effect animates something in, the cleanup function should reset the animation to the initial values:

```javascript
useEffect(() => {
  const node = ref.current;
  node.style.opacity = 1; // Trigger the animation
  return () => {
    node.style.opacity = 0; // Reset to the initial value
  };
}, []);
```

In development, opacity will be set to `1`, then to `0`, and then to `1` again. This should have the same user-visible behavior as setting it to `1` directly, which is what would happen in production. If you use a third-party animation library with support for tweening, your cleanup function should reset the tween's timeline to its initial state.

### Fetching data

If your Effect fetches something, the cleanup function should either abort the fetch or ignore its result:

```javascript
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```

You can't "undo" a network request that already happened, but your cleanup function should ensure that the fetch that's not relevant anymore does not keep affecting your application.



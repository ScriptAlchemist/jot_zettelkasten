# Escape Hatches

Some of your components may need to control and synchronize with systems outside of React. For example, you might need to focus an input using the browser API, play and pause a video player implemented without React, or connect and listen to messages from a remote server. In this chapter, you'll learn the escape hatches that let you "step outside" React and connect to external systems. Most of your application logic and data flow should not rely on these features.

> #### In this chapter
>
> * How to "remember" information without re-rendering
> * How to access DOM elements managed by React
> * How to synchronize components with external systems
> * How to remove unnecessary Effect from your components
> * How an Effect's life cycle is different from a component's
> * How to prevent some values from re-triggering Effects
> * How to make your Effect re-run less often
> * How to share logic between components

## Referencing values with refs

When you want a component to "remember" some information, but you don't want that information to `trigger new renders`, you can use a `ref`:

```javascript
const ref = useRef(0);
```

Like state, refs are retained by React between re-renders. However, setting state re-renders a components. Changing a ref does not! You can access the current value of that ref through the `ref.current` property.

```javascript
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

A ref is like a secret pocket of you component that React doesn't track. For example, you can use refs to store `timeout IDs`, `DOM elements`, and other objects that don't impact the component's rendering output.

## Manipulating the DOM with refs

React automatically updates the DOM to match your render output, so your components won't often need to manipulate it. However, sometimes you might need access to the DOM elements managed by React--for example, to focus a node, scroll to it, or measure its size and position. There is no built-in way to do those things in React, so you will need a ref to the DOM node. For example, clicking the button will focus the input using a ref:

```javascript
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

## Synchronizing with Effects

Some components need to synchronize with external systems. For example, you might want to control a non-React component based on the React state, set up a server connection, or send an analytics log when a component appears on the screen. Unlike event handlers, which let you handle particular events, Effects let you run some code after rendering. Use them to synchronize your component with some system outside of React.

Press Play/Pause multiple times and see how the video player stays synchronized to the `isPlaying` prop value:

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
  }, [isPlaying]);

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlaye
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

Many Effects also need to "clean up" after themselves. For example, if your Effect sets up a connection to a chat server, it should return a cleanup function that tells React how to disconnect your component from that server:

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

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

In development, React will immediately run and clean up your Effect one extra time. This is why you see `"✅ Connecting..."` printed twice. This ensures that you don't forget to implement the cleanup function.

## You Might Not Need An Effect

Effects are an escape hatch from the React paradigm. They let you "step outside" of React and synchronize your components with some external system. If there is no external system involved (for example, if you want to update a component's state when some props or state change), you shouldn't need an Effect. Removing unnecessary Effects will make your code easier to follow, faster to run, and less error-prone.

there are two common cases in which you don't need Effects:

* You don't need Effect to transform data for rendering.
* You don't need Effect to handle user events.

For example, you don't need an Effect to adjust some state based on other state:

```javascript
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🛑 Avoid: redundant state and unnecessary Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

Instead, calculate as much as you can while rendering:

```javascript
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

However, you do need Effect to synchronize with external systems.

## Life cycle of reactive effects

Effects have a different life cycle from components. Components may mount, update, or unmount. An Effect can only do two things: to start synchronizing something, and later to stop synchronizing it. this cycle can happen multiple times if your Effect depends on props and state that change over time.

This Effect depends on the value of the `roomId` prop. Props are reactive values, which means they can change on a re-render. Notice that the Effect re-synchronizes (and re-connects to the server) after you update the `roomId`:

`chat.js`:

```javascript
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

React provides a linter rule to check that you've specified your Effect's dependencies correctly. If you forget to specify `roomId` in the list of dependencies in the above example, the linter will find the bug automatically.

## Separating events from Effects

> #### Under Construction
>
> This section describes an experimental API that has not yet been added
> to React, so you can't use it yet.

Event handlers only re-run when you perform the same interaction again. Unlike event handlers, Effects re-synchronize if some value they read, like prop

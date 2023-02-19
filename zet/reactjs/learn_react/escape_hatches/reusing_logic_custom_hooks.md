# Reusing Logic with Custom Hooks

React comes with several built-in Hooks like `useState`, `useContext`,
and `useEffect`. Sometimes you'll wish that there was a Hook for some
more specific purpose: for example, to fetch data, to keep track of
whether the user is online, or to connect to a chat room. You might not
find these Hooks in React, but you can create your own Hooks for your
application's needs.

> #### You will learn
>
> * What custom Hooks are, and how to write your own
> * How to reuse logic between components
> * How to name and structure your custom Hooks
> * When and why to extract custom Hooks

## Custom Hooks: Sharing logic between components

Imagine you're developing an app that heavily relies on the network (as
most apps do. You want to warn the user if their network connection has
accidentally gone off while they were using your app. How would you do
about it?

It seems like you'll need two things in your component:

1. A piece of state that tracks whether the network is online.
2. An Effect that subscribes to the global `online` and `offline`
   events, and updated that state.

This will keep your component synchronized with the network status. You
might start with something like this:

```javascript
import { useState, useEffect } from 'react';

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    functin handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window. removeEventListener('offline', handleOffline);
    };
  }, []

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

Try turning your network on and off, and notice how this `StatusBar` updates in response to your actions.

Now imagine you also want to use the same logic in a different component. You want to implement a Save button that will become disabled and show "Reconnecting..." instead of "Save" while the network is off.

To start, you can copy and paste the `isOnline` state and the effect into `SaveButton`:

```javascript
import { useState, useEffect } from 'react';

export default function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

Verify that, if you turn off the network, the button will change its appearance.

These two components work fine, but the duplication in logic between them is unfortunate. It seems like even though they have different visual appearance, you want to reuse the logic between them.

## Extracting your own custom Hook from a component

Imagine for a moment that, similar to `useState` and `useEffect`, there was a built-in `useOnlineStatus` Hook. Then both of these components could be simplified an you could remove the duplication between them:

```javascript
function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline : 'Save Progress' : 'Reconnecting...'}
    </button>
  );
}
```

Although there is no such built-in Hook, you can write it yourself. Declare a function called `useOnlineStatus` and move all the duplicated code into if from the components you wrote earlier:

```javascript
function UseOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

At the end of the function, return `isOnline`. This lets your component read that value:

`App.js`:


```javascript
import { useOnlineStatus } from './useOnlneStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveclick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

`useOnlineStatus.js`:

```javascript
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

Verify that switching the network on and off updates both components.

Now your components don't have as much repetitive logic. More importantly, the code inside them describes what they want to do (use the online status!) rather than how to do it (by subscribing to the browser events).

When you  extract logic into custom Hooks, you can hide the gnarly details of how you deal with some external system of a browser API. The code of your components expresses your intent, not the implementation.

## Hook names always state with `use`

React applications are built from components. Components are built from Hooks, whether built-in or custom. You'll likely often use custom Hooks created by others, but occasionally you might write one yourself!

You must follow these naming conventions:

1. React components names must start with a capital letter, like `StatusBar` and `SaveButton`. React components also need to return something that React knows how to display, like a piece of JSX.
1. Hook names must start with `use` followed by a capital letter, like `useState` (built-in) or `useOnlineStatus` (custom, like earlier on the page). Hooks may return arbitrary values.

This convention guarantees that you can always look at a component and know where its state, Effects, and other React features might "hide". For example, if you see a `getColor()` function call inside your component, you can be sure that it can't possibly contain React state inside because its name doesn't start with `use`. However, a function call like `useOnlineStatus()` will most likely contains calls to other Hooks inside!

> #### Note
>
> If your linger is configured for React, it will enforce this naming
convention. Scroll up to the sandbox above and rename `useOnlineStatus`
to `getOnlineStatus`. Notice that the linter won't allow you to call
`useState` or `useEffect` inside of it anymore. Only Hooks and
components can call other Hooks!

---

> ##### Deep Dive
> #### Should all functions called during rendering start with the use prefix?
>
> No functions that don't call Hooks don't need to be Hooks.
>
> If your function doesn't call any Hooks, avoid the `use` prefix.
Instead, write it as a regular function without the `use` prefix. For
example, `useSorted` below doesn't call Hooks, so cal it `getSorted`
instead:

```javascript
// 🛑 Avoid: A Hook that doesn't use Hooks
function useSorted(items) {
  return items.slice().sort();
}

// ✅ Good: A regular function that doesn't use Hooks
function getSorted(items) {
  return items.slice().sort();
}
```

> This ensures that your code can call this regular function anywhere,
> including conditions:

```javascript
function List({ items, shouldSort }) {
  let displayeditems = items;
  if (shouldSort) {
    // ✅ It's ok to call getSorted() conditionally because it's not a hook
    displayedItems = getSorted(items);
  }
  // ...
}
```

> You should give `use` prefix to a function (and thus make it a Hook)
> if it uses at least on Hook inside of it:

```javascript
// ✅ Good: A Hook that uses other Hooks
function useAuth() {
  return useContext(Auth);
}
```

> Technically, this isn't enforced by React. In principle, you could make
> a Hook that doesn't call other Hooks. This is often confusing and
> limiting so it's best to avoid that pattern. However, there may be
> rare cases where it is helpful. For example, maybe your function
> doesn't use any Hooks right now, but you plan to add some Hooks calls
> to it in the future. Then it makes sense to name it with the `use`
> prefix:

```javascript
// ✅ Good: A Hook that will likely use some other Hooks later
function useAuth() {
  // TODO: Replace witht his line when authentication is implemented:
  // return useContext(Auth);
  return TEST_USER;
}
```

> Then components won't be able to call it conditionally. This will
> become important when you actually add Hook calls inside. If you don't
> plan to use Hooks inside it (now or later), don't make it a Hook.

## Custom Hooks let you share stateful logic, not state itself

In the earlier example, when you turned the network on and off, both components updated together. However, it's wrong to think that a single `isOnline` state variable is shared between them. Look at this code:

```javascript
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}
```

It works the same way as before you extracted the duplication:

```javascript
function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}

function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}
```

These are two completely independent state variables and Effects! They only happened to have the same value at the same time because you synchronized them with the same external value (Whether the network is on).

To better illustrate this, we'll need a different example. Consider this `Form` component:

```javascript
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('Mary');
  const [lastName, setLastName] = useState('Poppins');

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Last name:
        <input value={lastName} onChange={handleLastNamechange} />
      </label>
      <p><b>Good morning, {firstName} {lastName}.</b></p>
    </>
  );
}
```

There is some repetitive logic for each form field:

1. There's a piece of state (`firstName` and `lastName`).
2. There's a change handler (`handlefirstNameChange` and `handleLastNameChange`).
3. There's a piece of JSX that specifies the `value` and `onChange` attributes for that input.

You can extract the repetitive logic into this `useFormInput` custom Hook:

`App.js`:

```javascript
import { useFormInput } from './useFormInput.js';

export default function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');

  return (
    <>
      <label>
        First name:
        <input {...firstNameProps} />
      </label>
      <label>
        Last name:
        <input {...lastNameProps} />
      </label>
      <p><b>Good morning, {firstNameProps.value} {lastNameProps.value}.</b></p>
    </>
  );
}
```

`useFormInput.js`:

```javascript
import { useState } from 'react';

export function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  const inputProps = {
    value: value,
    onChange: handleChange
  };

  return inputProps;
}
```

Notice that it only declares one state variable called `value`.

However, the `Form` components calls `useFormInput` two times:

```javascript
function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');
  // ...
```

This is why it works like declaring two separate state variables!

Custom Hooks let you share stateful logic but not state itself. Each call to a Hook is completely independent from every other call to the same Hook. this is why to two sandboxes above are completely equivalent. If you'd like, scroll back up and compare them. The behavior before and after extracting a custom Hook is identical.

When you need to share the state itself between multiple components, lift it up and pass it down instead.

## Passing reactive values between Hooks

The code inside your custom Hooks will re-run during every re-render of your component. This is why, like components, custom Hooks need to be pure. Think of custom Hooks' code as part of your component's body!

Because custom Hooks re-render together with your component, they always receive the latest props and state. To see what this means, consider this chat room example. Change the server URL or the selected chat room:

`App.js`:

```javascript
import [ useState } from 'react'
import ChatRoom from './ChatRoom.js`;

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onchange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

`ChatRoom.js`:

```javascript
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, serServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.on('message', (msg) => {
      showNotification('New Messages: ' + msg);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

`chat.js`:

```javascript
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually be used
  if (typeof serverUrl !== 'string') {
    throw Error('Ecpected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expect roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log(`✅ Connecting to "${roomId}" room at ${serverUrl}...`);
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey');
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "${roomId}" room at ${serverUrl}`);
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

`notifications.js`:

```javascript
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotifications(message, theme = 'dark') {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black': 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

When you change `serverUrl` or `roomId`, the Effect "reacts" to your
changes and re-synchronizes. You can tell by the console messages that
the chat re-connects every time the you change your Effect's
dependencies.

Now move the Effect's code into a custom Hook:

```javascript
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

This lets your `ChatRoom` component call your custom Hook without
worrying about how it works inside:

```javascript
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e =>
        setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

This looks much simpler! (But it does the same thing.)

Notice that the logic still responds to prop and state changes. Try
editing the server URL or the selected room:

`App.js`:

```javascript
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

`ChatRoom.js`:

```javascript
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js':

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!<h1>
    </>
  );
}
```

`useChatRoom.js`:

```javascript
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

`chat.js`:

```javascript
export function createConnection({ serverUrl, roomId }) {
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey');
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

`notifications.js`:

```javascript
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme = 'dark') {
  Toastify({
    test: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white': 'black',
    },
  }).showToast();
}
```
Notice how you're taking the return value of one Hook:

```javascript
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234')

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

and pass it as an input to another Hook:

```javascript
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

Every time your `ChatRoom` component re-renders, it passes the latest
`roomId` and `serverUrl` to your Hook. This is why your Effect
re-connects to the chat whenever their values are different after a
re-render. (If you ever worked with music processing software, chaining
Hooks like this might remind you of chaining multiple audio effects,
like adding reverb or chorus. It's as if the output of `useState` "feeds
into" the input of the `useChatRoom`.)

## Passing event handlers to custom Hooks

> #### Under Construction
>
> This section describes an experimental API that has not yet been added
to React, so you can't use it yet.

As you start using `useChatRoom` in more components, you might want to
let different components customize its behavior. For example, currently,
the logic for what to do when a message arrives is hard coded inside the
Hook:

```javascript
exort function useChatRoom({ roomId, serverUrl }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const conneciton = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotifications('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

Let's say you want to move this logic back to your component:

```javascript
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });
  // ...
```

To make this work, change your custom Hook to take `onReceiveMessage` as
one of its named options:

```javascript
export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onReceiveMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl, onReceiveMessage]); // ✅ All dependencies declared
}
```

This will work, but there's one more improvement you can do when your custom Hook accepts event handlers.

Adding a dependency on `onReceiveMessage` is not ideal because it will cause the chat to re-connect every time the component re-renders. Wrap this event handler into an Effect Event to remove it from the dependencies:

```javascript
import { useEffect, useEffectEvent } from 'react'
//...

export function useChatRoom({ serverUrl, roomId, onRecieveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ All dependencies declared
}
```

Now the chat won't re-connect every time that the `ChatRoom` component re-renders. Here is a fully working demo of passing an event handler to a custom Hook that you can play with:

`App.js`:

```javascript
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

`ChatRoom.js`:

```javascript
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js';
import { showNotification } from './notification.js';

export default function ChatRoom({roomId}) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

`useChatRoom.js`:

```javascript
import { useState } from 'react';
import {experimental_useEffectEvent as useEffectEvent } from 'react';
import {createConnection} from './chat.js';

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onRecieveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

`chat.js`:

```javascript
export function createConnection({ serverUrl, roomId }) {
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey');
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

`notifications.js`:

```javascript
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme = 'dark') {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

Notice how you not longer need to know how `useChatRoom` works in order to use it. You could add it to any other component, pass any other options, and it would work the same way. That's the power of custom Hooks.

## When to use Custom Hooks

You don't need to extract a custom Hook for every little duplicated bit of code. Some duplication is fine. For example, extracting `useFormInput` Hook to wrap a single `useState` call like earlier is probably unnecessary.

However, whenever you write an Effect, consider whether it would be clearer to also wrap it in a custom Hook. You shouldn't need Effects very often, so if you're writing one, it means that you need to "step outside React" to synchronize with some external system or to do something that React doesn't have a built-in API for. Wrapping your Effect into a custom Hook lets you precisely communicated your intent and how the data flows through it.

For example, consider a `ShippingForm` component that displays two dropdowns: One shows the list of cities, and the other shows a list of areas in the selected city. You might start with some code that looks like this:

```javascript
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // This Effect fetches cities for country
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(res => res.json())
      .then(json => {
        if (@ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // This Effect fetches areas for the selected city
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(res => res.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]);

  // ...
```

Although this code is quite repetitive, it's correct to keep these Effects separate from each other. They synchronize two different thing, so you shouldn't merge them into one Effect. Instead, you can simplify the `ShippingForm` component above by extracting the common logic between then into your own `useData` Hook:

```javascript
function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    if (url) {
      let ignore = false;
      fetch(url)
        .then(res => res.json())
        .then(json => {
          if (!ignore) {
            setData(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [url]);
  return data;
}
```

Now you can replace both Effects in the `ShippingForm` component with calls to `useData`:

```javascript
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
  //...
```

Extracting a custom Hook makes the data flow explicit. You feed the `url` in and you get the `data` out. But "hiding" your Effect inside `useData`, you also prevent someone working on the `ShippingForm` component from adding unnecessary dependencies to it. Ideally, with time, most of your app's Effects will be in custom Hooks.

> ##### Deep Dive
> #### Keep you custom Hooks focused on concrete high-level use cases
> Start by choosing your custom Hook's name. If you struggle to pick a
clear name, it might mean that your Effect is too coupled to the rest of
your component's logic, and is not yet ready to be extracted.
>
> Ideally, your custom Hook's name should be clear enough that even a
person who doesn't write code often could have a good guess about what
your custom Hook does, what it takes, and what it returns:
>
> * ✅ `useData(url)`
> * ✅ `useImpressionLog(eventName, extraData)`
> * ✅ `useChatRoom(options)`
>
> When you synchronize with an external system, your custom Hook names
> may be more technical and use jargon specific to that system. It's
> good as long as it would be clear to the person familiar with that
> system:
>
> * ✅ `useMediaQuery(query)`
> * ✅ `useSocket(url)`
> * ✅ `useIntersectionObserver(ref, options)`
>
> Keep custom Hooks focused on concrete high-level use cases. Avoid
> creating and using custom "lifecycle" Hooks that act as alternatives
> and convenience wrappers for the `useEffect` API itself:
>
> * 🛑 `useMount(fn)`
> * 🛑 `useEffectOnce(fn)`
> * 🛑 `useUpdateEffect(fn)`
>
> For example, this `useMount` Hook tries to ensure some code only runs
> "on mount"

```javascript
function ChatRoom({ roomId }) {
  const [serverUrl, serServerUrl] = useState('https://localhost:1234');

  // 🛑 Avoid: using custom "lifecycle" Hooks
  useMount(() => {
    const connection = createConnection({ roomId, serverUrl });
    connection.connect();

    post('/analytics/event', { eventName: 'visit_chat' });
  });
  // ...
}

// 🛑 Avoid: creating custom "lifecycle" Hooks
function useMount(fn) {
  useEffect(() ={
    fn();
  }, []); // 🛑 React Hook useEffect has a missing dependency: 'fn'
}
```

> Custom "lifecycle" Hooks like `useMount` don't fit well into the React
> paradigm. For example, this code example has a mistake (it doesn't
> "react" to `roomId` or `serverUrl` changes), but the linter won't warn
> you about it because the linter only checks direct `useEffect` calls.
> It won't know about your Hook.
>
> If you're writing an Effect, start by using the React API directly:

```javascript
function ChatRoom({ roomId }) {
  const [serverUrl, serServerUrl] = useState('https://localhost:1234');

  // ✅ Good: two raw effects Separating by purpose

  useEffect(() => {
    const connection = createConnection({ serverUrl, roomId });
    connection.connect();
    return () => connection.disconnect();
  }, [serverUrl, roomId]);

  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_chat', roomId });
  }, [roomId]);
  //...
}
```

> Then, you can (but don't have to) extract custom Hooks for different
> high-level use cases:

```javascript
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // ✅ Great: custom Hooks named after their purpose
  useChatRoom({ serverUrl, roomId });
  useImpressionLog('visit_chat', { roomId });
  // ...
}
```

> A good custom Hook makes the calling code more declarative by
> constraining what it does. For example, `useChatRoom(options)` can be
> only connect to the chat room, while `useImpressionLog(eventName,
> extraData)` can only send an impression log to the analytics. If your
> custom Hook API doesn't constrain the use cases and is very abstract,
> in the long run it's likely to introduce more problems than it solves.

## Custom Hooks help you migrate to better patterns

Effects are an "escape hatch": you use them when you need to "step outside React" and when there is not better built-in solution for your use case. With time, the React team's goal is to reduce the number of the Effects in your app to the minimum by providing more specific solutions to more specific problems. Wrapping Effects in custom Hooks makes it easier to upgrade your code when these solutions become available. Let's return to this example:

`useOnlineStatus.js`:

```javascript
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventLIstener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

`App.js`:

```javascript
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

In the above example, `useOnlineStatus` is implemented with a pair of `useState` and `useEffect`. However, this isn't the best possible solution. There is a number of edge cases it doesn't consider. For example, it assumes that when the component mounts, `isOnline` is already `true`, but this may be wrong if the network already when offline. You can use the browser `navigator.onLIne` API to check for that, but using it directly would break if you run your React app on the server to generate the initial HTML. In short, this code could be improved.

Luckily, React 18 includes a dedicated API called `useSyncExternalStore` which takes care of all of these problems for you. Here is how your `useOnlineStatus` Hook, rewritten to take advantage of this new API:

```javascript


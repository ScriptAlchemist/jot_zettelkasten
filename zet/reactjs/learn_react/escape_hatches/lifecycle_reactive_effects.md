# Lifecycle of Reactive Effects

Effects have a different lifecycle from components. Components may
mount, update, or unmount. An Effect can only do two things: to start
synchronizing something, and later to stop synchronizing it. This cycle
can happen multiple times in your Effect depends on props and state that
change over time. React provides a linter rule to check that you've
specified your Effect's dependencies correctly. This keeps your Effect
synchronized to the latest props and state.

> #### You will learn
>
> * How an Effects' lifecycle is different from a component's lifecycle
> * How to think about each individual Effect in isolation
> * When you Effect needs to re-synchronize, and why
> * How your Effect's dependencies are determined
> * What it means for a value to be reactive
> * What an empty dependency array means
> * How React verifies your dependencies are correct with a linter
> * What to do when you disagree with the linter


## The lifecycle of an Effect

Every React component goes through the same lifecycle:

* A component mounts when it's added to the screen.
* A component updates when it receives new props or state. This usually
  happens in response to an interaction.
* A component unmounts when it's removed from the screen.

It's a good way to think about components, but not about Effects.
Instead, try to think about each Effect independently from your
component's lifecycle. An Effect describes how to synchronize an
external system to the current props and state. As your code changes,
this synchronization will need to happen more or less often.

To illustrate this point, consider this Effect connection your component
to a chat server:

```javascript
const serverUrl = 'https://localhost:1234;

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

Your Effect's body specifies how to start synchronizing:

```javascript
// ..
const connection = createConnection(serverUrl, roomId);
connection.connect();
return () => {
  connection.disconnect();
};
// ...
```

The cleanup function returned by your Effect specifies how to stop
synchronizing:

```javascript
// ...
return () => {
  connection.disconnect();
};
// ...
```

Intuitively, you might think that React would start synchronizing when
your component mounts and stop synchronizing when your component
unmounts. However, this is not the end of the story! Sometimes, it may
also be necessary to start and stop synchronizing multiple times while
the component remains mounted.

Let's look at why this is necessary, when it happens, and how you can
control this behavior.

> ### Note
>
> Some Effects don't return a cleanup function at all. More often than
> not, you'll want to return on--but if you don't, React will behave as if
> you returned an empty cleanup function that doesn't do anything.

## Why synchronization may need to happen more than once

Imagine this `ChatRoom` component receives a `roomId` prop that the user
picks in a dropdown. Let's say that initially the user picks the
`general` room as the `roomId`. Your app displays the `general` chat
room:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ rooId /* general */ }) {
  // ...
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

After the UI is displayed, React will run your Effect to start
synchronizing. It connects to the `general` room:

```javascript
function ChatRoom({ roomId /* general */ }) {
  useEffect(() => {
    const conenction = createConnection(serverUrl, roomId); // Connect
    to the general room
    connection.connect();
    return () => {
      connection.disconnect(); // Disconnects from the general room
    };
  }, [roomId]);
  // ...
```

So far, so good.

Later, the user picks a different room in the dropdown (for example,
`travel`). First, React will update the UI:

```javascript
function ChatRoom({ roomId /* travel */ }) {
  // ...
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

Pause to think about what should happen next. The user sees that
`travel` is the selected chat room in the UI. However, the Effect that
ran the last time is still connected to the `general` room. The `roomId`
prop has changed, so whatever your Effect did back then (connecting to
the `general` room) no longer matches the UI.

At this point, you want React to do two things:

1. Stop synchronizing with the old `roomId` (disconnect from the
   `general` room)
2. Start synchronizing with the new `roomId` (connect to the `travel`
   room)

Luckily, you've already taught React how to do both of these things!
Your Effects' body specifies how to start synchronizing, and your
cleanup function specifies how to stop synchronizing. All that React
needs to do now is to call them in the correct order and with the
correct props and state. Let's see how exactly that happens.

## How React re-synchronizes your Effect

Recall that your `ChatRoom` component has received a new value of its
`roomId` prop. It used to be `general`, and now it is `travel`. React
needs to re-synchronize your Effect to re-connect you to a different
room.

To stop synchronizing, React will call the cleanup function that your
Effect returned after connecting to the `general` room. Since `roomId`
was `general`, the cleanup function disconnects from the `general` room:

```javascript
function ChatRoom({ roomId /* general */ }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  // ...
}
```
Then React will run the Effect that you've provided during this render.
This time, `roomId` is `travel` so it will start synchronizing to the
`travel` chat room (until its cleanup function is eventually called
too):

```javascript
function ChatRoom({ roomId /* travel */ }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // connects
    to the travel room
    connection.connect();
    // ...
```

Thanks to this, you're now connected to the same room that the user
chose in the UI. Disaster averted!

Every time after your component re-renders with a different `roomId`,
your Effect will re-synchronize. For example, let's say the user changes
`roomId` from `travel` to `music`. React will again stop synchronizing
your Effect by calling its cleanup function (disconnecting you from the
`travel `room). Then is will start synchronizing again by running its
body with the new `roomId` prop (connecting you to the `music` room).

Finally, when the user goes to a different screen, `ChatRoom` unmounts.
Now there is not need to stay connected al all. React will stop
synchronizing your Effect one last time and disconnect you from the
`music` chat room.

## Thinking from the Effect's perspective.

Let's recap everything that's happened from the `ChatRoom` component's
perspective:

1. `ChatRoom` mounted with `roomId` set to `general`
2. `ChatRoom` updated with `roomId` set to `travel`
3. `ChatRoom` updated with `roomId` set to `music`
4. `ChatRoom` unmounted

During each of these points in the component's lifecycle, your Effect
did different things:

1. Your Effect connected to the `general` room
2. Your Effect disconnected form the `general` room and connected to the
   `travel` room
3. Your Effect disconnected from the `travel` room and connected to the
   `music` room
4. Your Effect disconnected from the `music` room

Now let's think about what happened from the perspective of the Effect
itself:

```javascript
useEffect(() => {
  // Your Effect connected to the room specified with roomId...
  const connection = createConnection(serverUrl, roomId);
  connection.connect();
  return () => {
    // ... until it disconencted
    connection.disconnect();
  };
}, [roomId]);
```

This code's structure might inspire you to see what happened as a
sequence of non-overlapping time periods:

1. Your Effect connected to the `general` room (until it disconnected)
2. Your Effect connected to the `travel` room (until it disconnected)
3. Your Effect connected to the `music` room (until is disconnected)

Previously, you were thinking from the component's perspective. When you
looked from the component's perspective, it was tempting to think of
Effects as "callbacks or "lifecycle events" that fire at specific time
like "after a render" or "before unmount". This way of thinking gets
complicated very fast, so it's best to avoid it.

Instead, always focus on a single start/stop cycle at a time. It
shouldn't matter whether a component is mounting, updating, or
unmouting. All you need to do is to describe how to start
synchronization and how to stop it. If you do it well, your Effect will
be resilient to being started and stopped as many times as it's needed.

This might remind you how you don't think whether a component is
mounting or updating when you write the rendering logic that creates
JSX. You describe what should be on the screen, and React figures out
the rest.

## How React verifies that your Effect can re-synchronize

Here is a live example that you can play with. Press "Open chat" to
mount the `ChatRoom` component:

`chat.js`:

```javascript
export function createConnection(serverUrl, roomId) {
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at '
      + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' +
      serverUrl);
    }
  };
}
```

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234;

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
  const [show, setShow] = useState(false);
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
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr/>}
      {show && <ChatRoom roomId={roomId} />}
    </>
  );
}
```

Notice that when the component mounts for the first time, you see three
logs:

1. `✅ Connecting to "general" room at
   https://localhost:1234...` (development-only)
2. `❌ Disconnected from "general" room at https://localhost:1234.`
   (development-only)
3. `✅ Connecting to "general" room at
   https://localhost:1234..`

The first two logs are development-only. In development, React always
remounts each component once. In other words, React verifies that your
Effect can re-synchronize by forcing it to do that immediately in
development. This might remind you how you might open the door and close
it an extra time to check that the door lock works. React starts and
stops your Effect one extra time in development to check you've
implemented its cleanup well.

The main reason your Effect will re-synchronize in practice is if some
data it uses has changed. In the sandbox above, change the selected chat
room. Notice how, when the `roomId` changes, your Effect
re-synchronizes.

However, there are also more unusual cases in which re-synchronization
is necessary. For example, try editing the `serverUrl` in the sandbox
above while the chat is open. Notice how the Effect re-synchronizes in
response to your edits to the code. In the future, React may add more
features that take advantage of re-synchronization.

## How React knows that it needs to re-synchronize the Effect

You might be wondering how React knew that your Effect needed to
re-synchronize after `roomId` changes. It's because you told React that
this Effect's code depends on `roomId` by including it in the list of
dependencies:

```javascript
function ChatRoom({ roomId }) // The roomId prop may change over time
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // This
    effect runs on roomId change
    connection.connect();
    return () => {
      conneciton.disconnect();
    };
  }, [roomId]); // So you tell React that his Effect "depends on" roomId
  // ...
}
```

Here's how this works:

1. You knew `roomId` is a prop, which means it can change over time.
2. You knew that your Effect reads `roomId` (so its logic depends on a
   value that may change later).
3. This is why you specified it as your Effect's dependency (so that it
   re-synchronizes when `roomId` changes).

Every time after your component re-renders, React will look at the array
of dependencies that you have passed. If any of the values in the array
is different from the value at the same spot that you passed during the
previous render, React will re-synchronize your Effect. For example, if
you passed `["general"]` during the initial render, and later you passed
`["travel"]` during the next render, React will compare `general` and
`travel`. These are different values (compared with `Object.is`), so
React will re-synchronize your Effect. On the other hand, if your
component re-renders but `roomId` had not changed, your Effect will
remain connected to the same room.

## Each Effect represents a separate synchronization process

Resist adding unrelated logic to your Effect only because this logic
need to run at the same time as an Effect you already wrote. For
example, let's say you want to send an analytics event when the user
visits the room. You already have an Effect that depends on `roomId`,
so you might feel tempted to add the analytics call right there:

```javascript
function ChatRoom({ roomId }) {
  useEffect(() => {
    logVisit(roomId);
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

But imagine you later add another dependency to this Effect that needs
to re-establish the connection. If this Effect re-synchronizes, it will
also call `logVisit(roomId)` for the same room, which you did not
intend. Logging the visit is a separate process from connecting. This is
why they should be written as two separate Effects:

```javascript
function ChatRoom({ roomId }) {
  useEffect(() => {
    logVisit(roomId);
  }, [roomId]);

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    // ...
  }, [roomId]);
  // ...
}
```

Each Effect in your code should represent a separate and independent
synchronization process.

In the above example, deleting one Effect wouldn't break the other
Effect's logic. This is a good indication that they synchronize
different things, and so it made sense to split them up. On the other
hand, if you split up a cohesive piece of logic into separate Effects, the
only code may look "cleaner" but will be more difficult to maintain.
This is why you should think whether the processes are same or separate,
not whether the code looks cleaner.

## Effects "react" to reactive values

Your Effect reads two variables (`serverUrl` and `roomId`), but you only
specified `roomId` as a dependency:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

Why doesn't `serverUrl` need to be dependency?

This is because the `serverUrl` never changes due to a re-render. It's
always the same no matter how many times and with which props and state
the component re-renders. Since `serverUrl` never changes, it wouldn't
make sense to specify it as a dependency. After all, dependencies only
do something when they change over time!

On the other hand, `roomId` may be different on a re-render. Props,
state, and other values declared inside the component are reactive
because they're calculated during rendering and participate in the React
data flow.

If `serverUrl` was a state variable, it would be reactive. Reactive
values must be included in dependencies:

```javascript
function ChatRoom({ roomId }) { // Props change over time
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId, serverUrl]);
  // ...
}
```

By including `serverUrl` as a dependency, you ensure that the Effect
re-synchronizes after it changes.

Try changing the selected chat room or edit the server URL in this
sandbox:

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

function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

Whenever you change a reactive value like `roomId` or `serverUrl`, the
Effect re-connects to the chat server.

## What an Effect with empty dependencies means

What happens if you move both `serverUrl` and `roomId` outside the
component?

```javascript
const serverUrl = 'https://localhost:1234';
const roomId = 'general';

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []); // ✅ All dependencies declared
  // ...
}
```

Now your Effect's code does not use any reactive values, so its
dependencies can be empty(`[]`).

If you think from the component's perspective, the empty `[]` dependency
array means this Effect connects to the chat room only when the
component mounts, and disconnects only when the component unmounts.
(Keep in mind that React would still re-synchronize in an extra time in
development to stress-test your Effect's logic.)

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
const roomId = 'general';

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr />}
      {show && <ChatRoom />}
    </>
  );
}
```

However, if you think from the Effect's perspective, you don't need to think about mounting and unmounting at all. What's important is you've specified what your Effect does to start and stop synchronizing. Today, it has not reactive dependencies. But if you ever want the user to change `roomId` or `serverUrl` over time (and so they'd have to become reactive), your Effect's code won't change. You will only need to add them to the dependencies.

## All variables declared in the component body are reactive

Props and state aren't the only reactive values. Values that you calculate from them are also reactive. If the props or state change, your component will re-render, and the values calculated from them will also change. This is why all variables from the component body used by the Effect should also be in the Effect dependency list.

Let's say that the user can pick a chat server in the dropdown, but they can also configure a default server in settings. Suppose you've already put the settings state in a context so you read the `settings` from that context. Now you can calculate the `serverUrl` based on the selected server from props and the default server from context:

```javascript
function ChatRoom({ roomId, selectedServerUrl }) { // roomId is reactive
  const settings = useContext(SettingsContext); // settings is reactive
  const serverUrl = selectedServerUrl ?? settings. defaultServerUrl; //
  serverUrl is reactive
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); 
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId, serverUrl]); // so it needs to re-synchronize when either
  of these values change.
  // ...
}
```

In this example, `serverUrl` is not a prop or a state variable. It's a
regular variable that you calculate during rendering. But it's
calculated during rendering, so it can change due to a re-render. This
is why it's reactive.

All values inside the component (including props, state, and variables
in your component's body) are reactive. Any reactive value can change on
a re-render, so you need to include reactive values as Effect's
dependencies.

In other words, Effects "react" to all values from the component body.

> ##### Deep Dive
> #### Can global or mutable values be dependencies?
>
> Mutable values (including global variables) aren't reactive.
>
> A mutable value like `location.pathname` can't be a dependency. It's
> mutable, so it can change at any time completely outside of the React
> rendering data flow. Changing it wouldn't trigger a re-render of you
> component. Therefore, even if you specified it in the dependencies,
> React wouldn't know to re-synchronize the Effect when it changes. This
> also breaks the rules of React because reading mutable data during
> rendering (which is when you calculate the dependencies) breaks purity
> of rendering. Instead, you should read and subscribe to an external
> mutable value with `useSyncExternalStore`.
>
> A mutable value like `ref.current` or things you read from it also
can't be a dependency. The ref object returned by `useRef` itself can
> be a dependency, but it's `current` property is intentionally
> mutable. It lets you `keep track of something without triggering a
> re-render`. But since changing it doesn't trigger a re-render, it's not
> a reactive value, and React won't know to re-run your Effect when it
> changes.
>
> As you'll learn below on this page, a linter will check for these
issues automatically


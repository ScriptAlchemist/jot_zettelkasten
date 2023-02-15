# Removing Effect Dependencies

When you write an Effect, the linter will verify that you've included
every reactive value (like props and state) that the Effect reads in the
list of your Effect's dependencies. This ensures that your Effect
remains synchronized with the latest props and state of your component.
Unnecessary dependencies may cause your Effect to run too often, or even
create an infinite loop. Follow this guide to review and remove
unnecessary dependencies from your Effects.

> #### You will learn
>
> * How to fix infinite Effect dependency loops
> * What to do when you want to remove a dependency
> * How to read a value from your Effect without "reacting" to it
> * How and why to avoid object and function dependencies
> * why suppressing the dependency linter is dangerous, and what to do instead

## Dependencies should match the code

When you write an Effect, you first specify how to start and stop
whatever you want your Effect to be doing:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  	// ...
}
```

Then, if you leave the Effect dependencies empty (`[]`), the linter will
suggest the correct dependencies:

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
    const connecction = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // <-- Fix the mistake here!
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

Fill them in according to what the linter says:

```javascript
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
}
```

Effects "react" to reactive values. Since `roomId` is a reactive value
(it can change due to a re-render), the linter verifies that you've
specified it as a dependency. If `roomId` receives a different value,
React will re-synchronize your Effect. This ensures that the chat stays
connected to the selected room and "reacts" to the dropdown:

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
    const connecction = createConnection(serverUrl, roomId);
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

## To remove a dependency, prove that it's not a dependency

Notice that you can't "choose" the dependencies of your Effect. Every
`reactive value` used by your Effect's code must be declared in your
dependency list. Your Effect's dependency list is determined by the
surrounding code:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) { // This is a reactive value
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // This uses the reactive value
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ So you must specify that reactive values as a dependency of your Effect
  // ...
}
```

Reactive values include props and all variables and functions declared
directly inside of your component. Since `roomId` is a reactive value,
you can't remove it from the dependency list. The linter wouldn't allow
it:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // 🛑 React Hook useEffect has a missing dependency: 'roomId'
  // ...
}
```
And the linter would be right! Since `roomId` may change over time, this
would introduce a bug in your code.

To remove a dependency, you need to "prove"to the linter that it doesn't
need to be a dependency. For example, you can move `roomId` out of your
component to prove that it's not reactive and won't change on
re-renders:

```javascript


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
const serverUrl = 'https://localhost:1234';
const roomId = 'music'; // Not a reactive value anymore

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ All dependencies declared
  // ...
}
```

Now that `roomId` is not a reactive value (and can't change on a re-render), it doesn't need to be a dependency:

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
const roomId = 'music';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

This is why you could now specify an empty (`[]`) dependency list. Your Effect really doesn't depend on any reactive value anymore, so it really doesn't need to re-run when any of the component's props or state change.

### To change the dependencies, change the code

You might have noticed a pattern in your workflow:

1. First, you change the code of your Effect or how your reactive values are declared.
2. Then, you follow the linter and adjust the dependencies to match the code you have changed.
3. If you're not happy with the list of dependencies, you go back to the first step (and change the code again)

The last part is important. If you want to change the dependencies, change the surrounding code first. You can think of the dependency list as a list of all the reactive values used by your Effect's code. You don't intentionally choose what to put on that list. The list describes your code. To change the dependency list, change the code.

This might feel like solving an equation. You might start with a goal (for example, to remove a dependency), and you need to "find" the exact code matching that goal. Not everyone finds solving equations fun, and the same thing could be said about writing Effects! Luckily, there is a list of common recipes that you can try below.

> #### Pitfall
>
> If you have an existing codebase, you might have some Effects that suppress the linter like this:

```javascript
useEffect(() => {
  // ...
  // 🛑 Avoid suppressing the linter like this:
  // eslint-ignore-next-line react-hooks/exhaustive-dependencies
}, []);
```

> When dependencies don't match the code, there is a very high risk of
> introducing bugs. By suppressing the linter, you "lie" to React about
> the values your Effect depends on. Instead, use the techniques below.

---

> ##### Deep Dive
> #### Why is suppressing the dependency linter so dangerous?
>
> Suppressing the linter leads to very unintuitive bugs that are hard to
find and fix. Here's one example:

```javascript
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  function onTick() {
    setCount(count + increment);
  }

  useEffect(() => {
    const id = setInterval(onTick, 1000);
    return () => clearInterval(id);
    // eslint-disable-next-line react-hooks/exhaustive-dependencies
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>-</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

> Let's say that you wanted to run the Effect "only on mount". You've
read that empty (`[]` dependencies do that, so you've decided to ignore
the linter, and forcefully specified `[]` as the dependencies.
>
> This counter was supposed to increment every second by the amount
configurable with the two buttons. However, since you "lied" to React
that this Effect doesn't depend on anything, React forever keeps using
the `onTick` function from the initial render. During that render,
`count` was `0` and `increment` was `1`. This is why `onTick` from that
render always calls `setCount(0 + 1)` every second, and you always see
`1`. Bugs like this are harder to fix when they're spread across
multiple components.
>
> There's always a better solution than ignoring the linter! To fix
this code, you need to add `onTick` to the dependency list. (To ensure
the interval is only setup once, make `onTick` an Effect Event.)
>
> We recommend to treat the dependency lint error as a compilation
error. If you don't suppress it, you will never see bugs like this. The
rest of this page documents the alternatives for this and other cases.

## Removing unnecessary dependencies

Every time you adjust the Effect's dependencies to reflect the code,
look at the dependency list. Does it makes sense for the Effect to
re-run when any of these dependencies change? Sometimes, the answer is
"no":

* Sometimes, you want to re-execute different parts of your Effect under
  different conditions.
* Sometimes, you want to only read the latest value of some
  dependencies instead of "reacting" to its changes.
* Sometimes, a dependency may change too often unintentionally because
  it's an object or a function.

To find the right solution, you'll need to answer a few questions about
your Effect. Let's walk through them.

### Should this code move to an event handler?

The first thing you should think about is whether this code should be an
Effect at all.

Imagine a form. On submit, you set the `submitted` state variable to
`true`. You need to send a POST request and show notification. You've
decided to put this logic inside an Effect that "reacts" to `submitted`
being `true`:


```javascript
function Form() {
  const [submitted, setSubmitted] = useState(false);

  useEffect(() => {
    if (submitted) {
      // 🛑 Avoid: Event-specific logic inside and Effect
      post('/api/register');
      showNotification('Successfully registered!');
    }
  }, [submitted]);

  function handleSubmit() {
    setSubmitted(true);
  }

  // ...
}
```

Later, you want to style the notification message according to the current theme, so you read the current theme. Since `theme` is declared in the component body, it is a reactive value, and you must declare it as a dependency:

```javascript
function Form() {
  const [submitted, setSubmitted] = useState(false);
  const theme = useContext(ThemeContext);

  useEffect(() => {
    if (submitted) {
      // 🛑 Avoid: Event-specific logic inside an Effect
      post('/api/register');
      showNotification('Successfully registered!', theme);
    }
  }, [submitted, theme]); // ✅ All dependencies declared

  function handleSubmit() {
    setSubmitted(true);
  }
  // ...
}
```

But by doing this, you've introduced a bug. Imagine you submit the form first and them switch between Dark and Light themes. The `theme` will change, the Effect will re-run, and so it will display the same notification again!

The problem here is that this shouldn't be an Effect in the first place.
You want to send this POST request and show the notification in response
to submitting the form, which is a particular interaction. When you wan
to run some code in response to a particular interaction, put that logic
directly into the corresponding event handler:

```javascript
function Form() {
  const theme = useContext(ThemeContext);

  function handleSubmit() {
    // ✅ Good: Event-specific logic is called from event handlers
    post('/api/register');
    showNotification('Successfully registered!', theme);
  }
  //...
}
```

Now that the code is an event handler, it's not reactive--so it will only run when the user submits the form. Read more about choosing between event handlers and Effects and how to delete unnecessary Effects.

### Is you Effect doing several unrelated things?

The next question you should ask yourself is whether your Effect is doing several unrelated things.

Imagine you're creating a shipping for where the user needs to choose their city and area. You fetch the list of `cities` from the server according to the selected `country` so that you can show them as dropdown options:

```javascript
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(res => res.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]); // ✅ All dependencies declared
  // ...
```

This is a good example of fetching data in an Effect. You are synchronizing the `cities` state with the network according to the `country` prop. You can't do this in an event handler because you need to fetch as soon as `ShippingForm` is displayed and whenever the `country` changes (no matter which interaction causes it).

Now let's say you're adding a second select box for city areas, which should fetch the `areas` for the currently selected `city`. You might start by adding a second `fetch` call for the list of areas inside the same Effect:

```javascript
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(res = > res.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    // 🛑 Avoid: A single Effect synchronizes two independent processes
    if (city) {
      fetch(`/api/areas?city=${city}`);
        .then(res => res.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
    }
    return () => {
      ignore = true;
    };
  }, [country, city]); // ✅ All dependencies declared
  // ...
```

However, since the Effect now uses the `city` state variable, you've had to add `city` to the list of dependencies. That, in turn, has introduced a problem. Now, whenever the user selects a different city, the Effect will re-run and call `fetchCities(country)`. As a result, you will be unnecessarily re-fetching the list of cities many times.

The problem with this code is that you're synchronizing two different unrelated things:

1. You want to synchronize the `cities` state to the network based on the `country` prop.
2. You want to synchronize the `areas` state to the network based on the `city` state.

Split the logic into two Effects, each of which reacts to the props that it needs to synchronize with:

```javascript
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(res => res.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]); // ✅ All dependencies declared

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
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
  }, [city]); // ✅ All dependencies declared
  // ...
```

Now the first Effect only re-runs if the `country` changes, while the second Effect re-runs when the `city` changes. You've separated them by purpose: two different things are synchronized by two separate Effects. Two separate Effects have two separate dependency lists, so they will no longer trigger each other unintentionally.

The final code is longer than the original, but splitting these Effects is still correct. Each Effect should represent an independent synchronization process. In this example, deleting one Effect doesn't break the other Effect's logic. This is a goo indication that they synchronize different things, and it made sense to split them up. If the duplication feels concerning, you can further improve this code by extracting repetitive logic into custom Hook.

### Are you reading some state to calculate the next state?

This Effect updates the `messages` state variable with a newly created array every time a new message arrives:

```javascript
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMsg) => {
      setMessages([...messages, newMsg]);
    });
    // ...
```

It uses the `messages` variable to create a new array starting with all the existing messages and adds the new message at the end. However, since `messages` is a reactive value read by an Effect, it must be a dependency:

```javascript
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMsg) => {
      setMessages([...messages, newMsg]);
    });
    return () => connection.disconnect();
  }, [roomId, messages]); // ✅ All dependencies declared
  // ...
```

And making `messages` a dependency introduces a problem.

Every time you receive a message, `setMessages()` causes the component to re-render with a new `messages` array that includes the received message. However, since this Effect now depends on `messages`, this will also re-synchronize the Effect. So every new messages will make the chat re-connect. The user would not like that!

To fix the issue, don't read the `messages` inside the Effect. Instead, pass an updater function to `setMessages`:

```javascript
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMsg) => {
      setMessages(msgs => [...msgs, newMsg]);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

Notice how your Effect does not read the `messages` variable at all now. You only need to pass an updater function like `msgs => [...msgs, receivedMessage]`. React puts your updater function in a queue and will provide the `msgs` argument to it during the next render. This is why the Effect itself doesn't need to depend on `messages` anymore. As a result of this fix, receiving a chat message will no longer make the chat re-connect.

### Do you want to read a value without "reacting" to its changes?

> #### Under Construction
>
> This section describes an experimental API that has not yet been added
> to React, so you can't use it yet.

Suppose that you want to play a sound when the user receives a new message unless `isMuted` is `true`:

```javascript
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMsg) => {
      setMessages(msgs => [...msgs, newMsg]);
      if (!isMuted) {
        playSound();
      }
    });
    // ...
```

Since your Effect now uses `isMuted` in its code, you have to add it to the dependencies:

```javascript
function ChatRoom({roomId}) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMsg) => {
      setMessages(msgs => [...msgs, newMsg]);
      if (!isMuted) {
        playSound();
      }
    });
    return () => connection.disconnect();
  }, [roomId, isMuted]); // ✅ All dependencies declared
  // ...
```

The problem is that every time `isMuted` changes (for example, when the user presses the "Muted" toggle), the Effect will re-synchronize, and reconnect to the chat server. This is not the desired user experience! (In this example, even disabling the linter would not work--if you do that, `isMuted` would get "stuck" with is old value.)

To solve this problem, you need to extract the logic that shouldn't be reactive out of the Effect. You don't want this Effect to "react" to the changes in `isMuted`. Move this non-reactive piece of logic into an Effect Event:

```javascript
import { useEffect, useState, useEventEffect } from 'react'

function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  const onMessage = useEffectEvent(receivedMessage => {
    setMessages(msgs => [...msgs, receivedMessage]);
    if (!isMuted) {
      playSound();
    }
  });

  useEffect(() => {
    const connecton = createConnection();
    connection.connect();
    connection.on('message', (newMsg) => {
      onMessage(newMsg);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  /...
```

Effect Events let you split an Effect into reactive parts (which should "react" to reactive values like `roomId` and their changes) and non-reactive parts (which only read their latest values, like `onMessage` reads `isMuted`). Now that you read `isMuted` inside an Effect Event, it doesn't need to be a dependency of your Effect. As a result, the chat won't re-connect when you toggle the "muted" setting on and off, solving the original issue!

### Wrapping an event handler from the props

You might run into a similar problem when your component receives an event handler as a prop:

```javascript
function ChatRoom({ roomId, onReceiveMessage }) {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMsg) => {
      onRecieveMessage(newMsg);
    });
    return () => connection.disconnect();
  }, [roomId, onReceiveMessage]); // ✅ All dependencies declare
  // ...
```

Supposed that the parent component passes a different `onReceiveMessage` function on every render:



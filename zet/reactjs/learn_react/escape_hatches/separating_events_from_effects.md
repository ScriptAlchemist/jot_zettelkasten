# Separating Events from Effects

Event handlers only re-run when you perform the same interaction again.
Unlike event handler, Effects re-synchronize if some value they read,
like a prop or a state variable, is different from what it was during
the last render. Somethings, you also want a mix for both behaviors: an
Effect that re-runs in response to some values but not others. This page
will teach you how to do that.

> #### You will learn
>
> * How to choose between an event handler and an Effect
> * Why Effects are reactive, and event handlers are not
> * What to do when you want a part of your Effect's code to not be
    reactive
> * What Effect Events are, and how to extract them from your Effects.
> * How to read the latest props and state from Effects using Effect
    Events

## Choosing between event handler and Effects

First, let's recap the difference between event handlers and Effects.

Imagine you're implementing a chat room component. Your requirements
look like this:

1. Your component should automatically connect to the selected chat
   room.
2. When you click the "Send" button, it should send a message to the
   chat.

Let's say you've already implemented the code for them, but you're not
sure where to put it. Should you use event handlers or Effects? Every
time you need to answer this question, consider why the code needs to
run.

## Event handlers run in response to specific interactions

From the user's perspective, sending a message should happen because the
particular "Send" button was clicked. The user will get rather upset if
you send their message at any other time or for any other reason. This
is why sending a message should be an event handler. Event handlers let
you handle specific interactions like clicks:

```javascript
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');
  // ...
  function handleClick() {
    sendMessage(message);
  }
  // ...
  return (
    <>
      <input value={message} onChange={e => setMessage(e.target.value)}
      />
      <button> onClick={handleClick}>Send</button>;
    </>
  );
}
```

With an event handler, you can be sure that `sendMessage(message)` will
only run if the user presses the button.

## Effects run whenever synchronization is needed

Recall that you also need to keep the component connected to the chat
room. Where does that code go?

The reason to run this code is not some particular interaction. It
doesn't matter why or how the user navigated to the chat room screen.
Now that they're looking at it and could interact with it, the component
needs to stay connected to the selected chat server. Even if the chat
room component was the initial screen of your app, and the user has not
performed any interactions at all, you would still need to connect. This
is why it's an Effect:

```
function ChatRoom({ roomId }) {
  // ...
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

With this code, you can be sure that there is always an active
connection to the currently selected chat server, regardless of the
specific interactions performed by the user. Whether the user has only
opened your app, selected a different room, or navigated to another
screen and back, your Effect will ensure that the component will remain
synchronized with the currently selected room, and will re-connect
whenever it's necessary.

`chat.js`:

```javascript
export function sendMessage(message) {
  console.log('🔵 You sent: ' + message);
}

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId '" room at ' + serverUrl + '...');
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
import { createConnection, sendMessage } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  function handleSendClick() {
    sentMessage(message);
  }

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input value={message} onChange={e => setMessage(e.target.value)}
      />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
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
      {show && <hr />}
      {show && <ChatRoom roomId={roomId} />}
    </>
  );
}
```

## Reactive values and reactive logic

Intuitively, you could say that event handlers are always triggered
"manually", for example by clicking a button. Effects, on the other
hand, are "automatic": they run and re-run as often as its' needed to
stay synchronized.

There is more precise ways to think about it.

Props, state, and variables declared inside your component's body are
called `reactive values`. In this example, `serverUrl` is not a reactive
value, but `roomId` and `message` are. They participate in the rendering
data flow:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');
  // ...
}
```

Reactive values like these can change due to a re-render. For example,
the use may edit the `message` or choose a different `roomId` in a
dropdown. Event handlers and Effects are different in how they respond
to changes:

* Logic inside event handlers is not reactive. It will not run again
  unless the user performs the same interaction (for example, a click)
  again. Event handlers can read reactive values, but they don't "react"
  to their changes.
* Logic inside Effects is reactive. If your Effect reads a reactive
  value, you have to specify it as a dependency. Then, if a re-render
  causes that value to change, React will re-run your Effect's logic
  again with the new value.

Let's revisit the previous example to illustrate this difference.

## Logic inside event handlers is not reactive

Take a look at this line of code. Should this logic be reactive or not?

```javascript
// ...
setMessage(message);
// ...
```

From the user's perspective, a change to the `message` does not mean
that they want to send a message. It only means that the user is typing.
In other words, the logic that sends a message should not be reactive.
It should not run again only because the reactive value has changed.
That's why you placed this logic in the event handler:

```javascript
function handleSendClick() {
  sendMessage(message);
}
```

Event handlers aren't reactive, so `sendMessage(message)` will only run
when the user clicks the send button.

## Logic inside Effects is reactive

Now let's return to these lines:

```javascript
// ...
const connection = createConnection(serverUrl, roomId);
connection.connect();
// ...
```
From the user's perspective, a change to the `roomId` does mean that
they want to connect to a different room. In other words, the logic for
connecting to the room should be reactive. You want these lines of code
to "keep up" with the reactive value, and to run again if that value is
different. That's why you put logic inside an Effect:

```javascript
useEffect(() => {
  const connection = createConnection(serverUrl, roomId);
  connection.connect();
  return () => {
    connection.disconnect();
  };
}. [roomId]);
```

Effects are reactive, so `createConnection(serverUrl, roomId)` and
`connection.connect()` will run for every distinct value of `roomId`.
Your Effect keeps the chat connection synchronized to the currently
selected room.

## Extracting non-reactive logic out of Effects

Things get more tricky when you want to mix reactive logic with
non-reactive logic.

For example, imagine that you want to show a notification when the user
connects to the chat. You read the current theme (dark or light) from
the props so that you can show the notification to the correct color:

```javascript
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    // ...
```

However, `theme` is a reactive value (it can change as a result of
re-rendering), and every reactive value read by an Effect must be
declared as its dependency. So now you have to specify `theme` as a
dependency of your Effect:

```javascript
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId, theme]); // ✅ All dependencies declared
    // ...
```

Play with this example and see if you can spot the problem with this
user experience:

`notification.js`:

```javascript
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
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

`chat.js`:

```javascript
import function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notification.js';

const serverUrl = 'https://localhost:1234':

function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnectio(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, theme]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
        <label>
          <input
            type="checkbox"
            checked={isDark}
            onChange={e => setIsDark(e.target.checked)}
          />
          Use Dark Theme
        </label>
        <hr />
        <ChatRoom
          roomId={roomId}
          theme={isDark ? 'dark' : 'light'}
        />
      </>
  );
}
```

When the `roomId` changes, the chat re-connects as you would expect. But
since `theme` is also a dependency, that chat also re-connects every
time you switch between the dark and the light theme. That's not great!

In other words, you don't want this line to be reactive, even though it
is inside an Effect (which is reactive):

```javascript
// ...
showNotification('Connected!', theme);
// ...
```

You need a way to separate this non-reactive logic from the reactive
Effect around it.

## Declaring an Effect Event

> #### Under construction
>
> This section describes an experimental API that has not yet been added
> to react, so you can't use it yet.

Use a special Hook called `useEffectEvent` to extract this non-reactive
logic out of your Effect:

```javascript
import { useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });
  // ...
```

Here, `onConnected` is called an Effect Event. It's a part of your
Effect logic, but it behaves a lot more like an event handler. The logic
inside it is not reactive, and it always "sees' the latest values of
your props and state.

Now you can call the `onConnected` Effect Event from inside your Effect:

```javascript
function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

This solves the problem. Note that you had to remove `onConnected` from
the list on your Effect's dependencies. Effect Events are not reactive
and must be omitted from dependencies. The linter will error if you
include them.

Verify that the new behavior works as you would expect:

`chat.js`:

```javascript
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimout(timeout);
    }
  };
}
```

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as UseEffectEvent } from react'
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notification.js';

const serverUrl = 'https://localhost:1234':

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnectio(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, theme]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
        <label>
          <input
            type="checkbox"
            checked={isDark}
            onChange={e => setIsDark(e.target.checked)}
          />
          Use Dark Theme
        </label>
        <hr />
        <ChatRoom
          roomId={roomId}
          theme={isDark ? 'dark' : 'light'}
        />
      </>
  );
}
```

You can thing of Effect Events as being very similar to event handlers.
The main difference is that event handlers run in response to a user
interactions, whereas Effect Events are trigger by you from Effects.
Effect Events let you "break the chain" between the reactivity of
Effects and some code that should be not reactive.

## Reading latest props and state with Effect Events

> #### Under Construction
>
> This section describes and experimental API that has not yet been added
to React, so you can't use it yet.

Effect Events let you fix many patterns where you might be tempted to
suppress the dependency linter.

For example, say you have an Effect to log the page visits:

```javascript
function Page() {
  useEffect(() => {
    logVisit();
  }, []);
  // ...
}
```

Later, you add multiple routes to your site. Now your `Page` component
receives a `url` prop with the current path. You want to pass the `url`
as a part of your `logVisit` call, but the dependency linter complains:

```javascript
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, []); // 🛑 React Hook useEffect has a missing dependency: 'url'
  // ...
}
```

Think about what you want the code to do. You want to log a separate
visit for different URLs since each URL represents a different page. In
order words, this `logVisit` call should be reactive with respect to the
`url`. This is why, in this case, it makes sens to follow the dependency
linter, and add `url` as a dependency:

```javascript
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, [url]); // ✅ All dependencies declared
  // ..
}
```

Now let's say you want to include the number of items in the shopping
cart together with every page visit:

```javascript
function Page({ url }) {
  const { tems } useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
  }, [url]); // 🛑 React Hook useEffect has a missing dependency:
  numbersOfItems
  // ...
}
```

You used `numberOfItems` inside the Effect, so the linter asks you to
add it as a dependency. However, you don't want the `logVisit` call to
be reactive with respect to `numberOfItems`. If the user puts something
into the shopping cart, and the `numberOfItems` changes, this does not
mean that the user visited the page again. In other words, visiting the
page feels similar to an event. You want to be very precise about when
you say it's happened.

Split the code into two parts:

```javascript
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]); // ✅ All dependencies declared
  // ...
}
```

Here, `onVisit` is an Effect Event. The code inside it isn't reactive.
This is why you can use `numberOfItems` (or any other reactive value!)
without worrying that it will cause the surrounding code to re-execute
on changes.

On the other hand, the Effect itself remains reactive. Code inside the
Effect uses the `url` prop, so the Effect will re-run after every
re-render with a different `url`. This, in turn will call the `onVisit`
Effect Event.

As a result, you will call `logVisit` for every change to the `url`, and
always ready the latest `numberOfItems`. However, if `numberOfItems`
changes on its own, this will not cause any of the code to re-run.

> #### Note
> You might be wondering if you could call `onVisit()` with no
arguments, an dread the `url` inside it:

```javascript
const onVisit = useEffectEvent(() => {
  logVisit(url, numberOfItems);
});

useEffect(() => {
  onVisit();
}, [url]);
```

> This would work, but it's better to pass this `url` to the Effect
> Event explicitly. By passing `url` as an argument to your Effect Event,
> you are saying that visiting a page with a different `url` constitutes a
> separate "event" from the user's perspective. The `visitUrl` is a part
> of the "event" that happened:

```javascript
const onVisit = useEffectEvent(visitedUrl => {
  logVisit(visitedUrl, numberOfItems);
});

useEffect(() => {
  onVisit(url);
}, [url]);
```

> Since your Effect Event explicitly "asks" for the `visitedUrl`, now
> you can't accidentally remove `url` from the Effect's dependencies. If
> you remove the `url` dependency (causing distinct page visits to be
> counted as one), the linter will warn you about it. You want `onVisit`
> to be reactive with regards to the `url`, to instead of reading the
> `url` inside (where it wouldn't be reactive), you pass it from your
> Effect.
>
> The becomes especially important if there is some asynchronous logic
> inside the Effect:

```javascript
const onVisit = useEffectEvent(visitedUrl => {
  logVisit(vistiedUrl, numberOfItems);
});

useEffect(() => {
  setTimout(() => {
    onVisit(url);
  }, 5000);
}, [url]);
```

> In this example, `url` inside `onVisit` corresponds to the latest
> `url` (which could have already changed), but `visitedUrl` corresponds
> to the `url` that originally caused this Effect (and this `onVisit`
> call) to run.

---

> ##### Deep Dive
> #### Is it okay to suppress the dependency linter instead?
>
> In the existing codebases, you may sometimes see the lint rule
suppressed like this:

```javascript
function Page({ url }) {
  const {items} = useContext(ShoppingCartContext);
  const numberOfItems = items. lenth;

  useEffect(() => {
    logVisit(url, numberOfItems);
    // :stop: Avoid suppresing the liner like this:
    // eslint-disabled-next-lin react-hooks/exhaustive-deps
  }, [url]);
  // ...
}
```

> After `useEffectEvent` becomes a stable part of React, we recommend to
never suppress the linter like this.
>
> The first downside of suppressing the rule is that React will not
longer warn you when your Effect needs to "react" to a new reactive
dependency you've introduced to your code. For example, in the earlier
example, you added `url` to the dependencies because React reminded you
to do it. You will no longer get such reminders for any future edits to
that Effect if you disable the linter. This leads to bugs.
>
> Here is an example of a confusing bug cause by suppressing the linter.
In this example, the `handleMove` function is supposed to read the
current `canMove` state variable value in order to decide whether the
dot should follow the cursor. However, `canMove` is always `true` inside
`handleMove`. Can you see why?

```javascript
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  function handleMove(e) {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  }

  useEffect(() => {
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
    // eslint-disabled-next-line react-hooks/exhaustive-deps
  }, []);

  return (
    <>
      <label>
        <input type="checkbox"
        check={canMove}
        onChange={e => setCanMove(e.target.checked)}
      />
      The dis is allowed to move
    </label>
    <hr />
    <div style={{
      position: 'absolute',
      backgroudColor: 'pink',
      borderRadius: '50%',
      opacity: 0.6,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}
```

> The problem with this code is in suppressing the dependency linter. If
> you remove the suppression, you'll see that his Effect should depend on
> the `handleMove` function. This makes sense: `handleMove` is declared
> inside the component body, which  makes it a reactive value. Every
> reactive value must be specified as a dependency, or it can potentially
> get stale over time!
>
> The author of the original code has "lied" to React by saying that the
Effect does not depend (`[]`) on any reactive values. This is why React
did not re-synchronize the Effect after `canMove` has changed (and
`handleMove` with it). Because React did not re-synchronize the Effect,
the `handleMove` attached as a listener is the `handleMove` function
created during the initial render. During the initial render, `canMove`
was `true`, which is why `handleMove` from the initial render will
forever see that value.
>
> If you never suppress the linter, you will never see problems with
stale values.
>
> With `useEffectEvent`, there is no need to "lie" to the linter, and
the code works as you would expect:

```javascript
import { useEffect, useState } from 'react'
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0});
  const [canMove, setCanMove] = useState(true);

  const onMove = useEventEffect(e => {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  });

  useEffect(() => {
    window.addEventListener('pointermove', onMove);
    return () => window.removeEventListener('pointermove', onMove);
  }, []);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.check)}
        />
        The dot is allowed to move
      </label>
      <hr >
      <div style={{
        postion: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

> This doesn't mean that `useEffectEvent` is always the correct
solution. You should only apply it to the lines of code that you don't
want to be reactive. For example, in the above sandbox, you didn't want
the Effect's code to be reactive with regards to `canMove`. That's why
it made sense to extract an Effect Event.
>
> Read Removing Effect Dependencies for other correct alternatives to
suppressing the linter

## Limitations of Effect Events

> #### Under Construction
>
> This section describes an experimental API that has not yet been added
to React, so you can't use it yet.

Effect Events are very limited in how yo can use them:

* Only call them from inside Effects.
* Never pass them to other components of Hooks.

For example, don't declare and pass an Effect Event like this:

```javascript
function Timer() {
  const [count, setCount] = useState(0);

  const onTick = useEffectEvent(() => {
    setCount(count + 1);
  });

  useTimer(onTick, 1000);

  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  useEffect(() => {
    const id = setInterval(() => {
      callback();
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay, callback]); // Need to specify "callback" in dependencies
}
```

Instead, always declare Effect Events directly next to the Effects that
use them:

```javascript
function Timer() {
  const [count, setCount] = useState(0);
  useTimer(() => {
    setCount(count + 1);
  }, 1000);
  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  const onTick = useEffectEvent(() => {
    callback();
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick(); // ✅ Good: only called locally inside an
      Effect
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay]); // Non eed to specifiy "onTick" (an Effect Event) as
  a dependency
}
```

Effect Events are non-reactive "pieces" of your Effect code. They should
be next to the Effect using them.

## Recap

* Event handlers run in response to specific interactions
* Effects run whenever synchronization is needed.
* Logic inside event handlers is not reactive.
* Logic inside Effects is reactive
* You can move non-reactive logic from Effects into Effect Events.
* Only call Effect Events from inside Effects.
* Don't pass Effect Events to other components or Hooks.

# Challenges

## Challenge 1 of 4: Fix a variable that doesn't update

This `Timer` component keeps a `count` state variable which increases
every second. The value by which it's increasing is stored into the
`increment` state variable. You can control the `increment` variable
with the plus and minus buttons.

However, no matter how many times you clock the plus button, the counter
is still incremented by one every second. What's wrong with this code?
Why is `increment` always equal to `1` inside the Effect's code? Find
the mistake and fix it.

```javascript
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
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
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

## Challenge 2 of 4: Fix a freezing counter

This `Timer` component keeps a `count` state variable which increases
every second. The value by which it's increasing is stored in the
`increment` state variable, which you can control it with the plus and
minus buttons. For example, try pressing the plus button nine times, and
notice that the `count` not increases each second by ten rather than by
one.

There is a small issue with this user interface. You might notice that
if you keep pressing the plus or minus buttons faster than once per
second, the timer itself seems to pause. It only resumes after a second
passes since the last time you've pressed either button. Find why this
is happening, and fix this issue so that the timer ticks on every second
without interruptions.

```javascript
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, [increment]);

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
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

## Challenge 3 of 4: Fix a non-adjustable delay

In this example, you can customize the interval delay. It's stored in a `delay` state variable which is updated by two buttons. However, every if you press the "plus 100ms" button until the `delay` is 1000 milliseconds (that is, a second), you'll notice that the timer still increments very fast (every 100ms). It's as if your changes to the `delay` are ignored. Find and fix the bug.

```javascript
eort { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);
  const [delay, setDelay] = useState(100);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  const onMount = useEffectEvent(() => {
    return setInterval(() => {
      onTick();
    }, delay);
  });

  useEffect(() => {
    const id = onMount();
    return () => {
      clearInterval(id);
    }
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
      <p>
        Increment delay:
        <button disabled={delay === 100} onClick={() => {
          setDelay(d => d - 100);
        }}>–100 ms</button>
        <b>{delay} ms</b>
        <button onClick={() => {
          setDelay(d => d + 100);
        }}>+100 ms</button>
      </p>
    </>
  );
}
```

## Challenge 4 of 4: Fix a delayed notification

When you join a chat room, this component shows a notification. However, it doesn't show the notification immediately. Instead, the notification is artificially delayed by two seconds so that the user has a chance to look around the UI.

This almost works, but there is a bug. Try changing the dropdown from "general" to "travel' and then to "music" very quickly. If you do it fast enough, you will see two notifications (as expected!) but they will both say "Welcome to music".

Fix it so that when you switch from "general" to "travel" and then to "music" very quickly, you see two notifications, the first one being "Welcome to travel" and the second on being "Welcome to music". (For an additional challenge, assuming you've already made the notifications show the correct rooms, change the code so that only the latter notification is displayed.)

`chat.js`:

```javascript
export default function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Welcome to ' + roomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      setTimeout(() => {
        onConnected();
      }, 2000);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

# Solutions

## Challenge 1 of 4:

As usual, when you're looking for bugs in Effects, start by searching for linter suppressions.

If you remove the suppression comment, React will tell you that this Effect's code depends on `increment`, but you "lied" to React by claiming that this Effect does not depend on any reactive values (`[]`). Add `increment` to the dependency array:

```javascript
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, [increment]);

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
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

Now, when `increment` changes, React will re-synchronize your Effect, which will restart the interval.

## Challenge 2 of 4:

The issues is that the code inside the Effect uses the `increment` state variable. Since it's a dependency of your Effect, every change to `increment` causes the Effect to re-synchronize, which causes the interval to clear. If you keep clearing the interval every time before it has a chance to fire, it will appear as if the timer has stalled.

To solve the issue, extract an `onTick` Effect Event from the Effect:

```javascript
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  })

  useEffect(() => {
    const id = setInterval(() => {
      onTick();
    }, 1000);
    return () => {
      clearInterval(id);
    };
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
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

Since `onTick` is an Effect Event, the code inside it isn't reactive. The change to `increment` does not trigger any Effects.

## Challenge 3 of 4:

The problem with the above example is that it extracted an Effect Event
called `onMount` without considering what the code should actually be
doing. You should only extract Effect Events for a specific reason: when
you want to make a part of your code non-reactive. However, the
`setInterval` call should be reactive with respect to the `delay` state
variable. If the `delay` changes, you want to set up the interval from
scratch! To fix this code, pull all the reactive code back inside the
Effect:

```javascript
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);
  const [delay, setDelay] = useState(100);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick();
    }, delay);
    return () => {
      clearInterval(id);
    }
  }, [delay]);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
      <p>
        Increment delay:
        <button disabled={delay === 100} onClick={() => {
          setDelay(d => d - 100);
        }}>–100 ms</button>
        <b>{delay} ms</b>
        <button onClick={() => {
          setDelay(d => d + 100);
        }}>+100 ms</button>
      </p>
    </>
  );
}
```

In general, you should be suspicious of functions like `onMount` that focus on the timing rather than the purpose of a piece of code. It may feel "more descriptive" at first but it obscures your intent. As a rule of thumb, Effect Events should correspond to something that happens from the user's perspective. Fro example, `onMessage`, `onTick`, `onVisit`, or `onConnected` are good Effect Event names. Code inside them would likely not need to be reactive. On the other hand, `onMount`, `onUpdate`, `onUnmount`, or `onAfterRender` are so generic that it's easy to accidentally put code that should be reactive into them. This is why you should name your Effect Events after what the user thinks has happened, not when some code happened to run.

## Challenge 4 of 4:

Inside your Effect Event, `roomId` is the value at the time Effect Event was called.

Your Effect Event is called with a two second delay. If you're quickly switching from the travel to the music room, by the time the travel room's notification shows, `roomId` is already `"music"`. This is why both notifications say "Welcome to music".

To fix the issue, instead of reading the latest `roomId` inside the Effect Event, make it a parameter of your Effect Event, like `connectedRoomId` below. Then pass `roomId` from your Effect by calling `onConnected(roomId)`:

```javascript
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(connectedRoomId => {
    showNotification('Welcome to ' + connectedRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      setTimeout(() => {
        onConnected(roomId);
      }, 2000);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

The Effect that had `roomId` set to `travel` (so it connected to the "travel" room) will show the notification for "travel". The Effect that had `roomId` set to "music" (so it connected to the "music" room) will show the notification for "music". In other words, `connectedRoomId` comes from your Effect (which is reactive), while `theme` always uses the latest value.

To solve the additional challenge, save the notification timeout ID and clear it in the cleanup function of your Effect:

```javascript
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(innerRoomId => {
    showNotification('Welcome to ' + innerRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    let connectionId;
    connection.on('connected', () => {
      connectionId = setTimeout(() => {
        onConnected(roomId);
      }, 2000);
    });
    connection.connect();
    return () => {
      connection.disconnect();
      clearTimeout(connectionId);
    }
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

This ensures that already scheduled (but not yet displayed) notifications get cancelled when you change rooms.

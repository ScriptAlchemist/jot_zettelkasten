# You Might Not Need an Effect

Effects are an escape hatch from React paradigm. They let you "step
outside" of React an synchronize your components with some external
system like a non-React widget, network, or the browser DOM. If there is
no external system involved (for example, if you want to update a
component's state when some props or state change), you shouldn't need
an Effect. Removing unnecessary Effect will make your code easier to
follow, faster to run, and less error-prone.

> ### You will learn
>
> * Why and how to remove unnecessary Effect form your components
> * How to cache expensive computations without Effects
> * How to reset and adjust component state without Effects
> * How to share logic between event handlers
> * Which logic should be moved to event handlers
> * How to notify parent components about changes

## How to remove unnecessary Effects

There are two common cases in which you don't need Effects:

* You don't need Effects to transform data for rendering. For example, let's say you want to filter a list before displaying it. You might feel tempted to write an Effect that updates a state variable when the list changes. However, this is inefficient. When you update your component's state, React will first call your component functions to calculate what should be on the screen. Then React will "commit" these changes to the DOM, updating the screen. Then React will run your Effects. If your Effect also immediately updates the state, this restarts the whole process form scratch! To avoid the unnecessary render passes, transform all the data at the lop level of your components. That code will automatically re-run whenever your props or state change.
* You don't need Effect to handle user events. For example, let's say you want to send an `/api/buy` POST request and show a notification when the user buys a product. In the Buy button click event handler, you know exactly what happened. By the time an Effect runs, you don't know what the user did (for example, which button was clicked). This is why you'll usually handle user events in the corresponding event handlers.

You do need Effects to synchronize with external systems. For example, you can write an Effect that keeps a jQuery widget synchronized with the React state. you can also fetch data with Effects: for example, you can synchronize the search results with the current search query. Keep in mind that modern frameworks provide more efficient built-in data fetching mechanisms than writing Effects directly in your components.

To help you gain the right intuition, let's look at some common concrete examples!

## Updating state based on props or state

Suppose you have a component with two state variables: `firstName` and `lastName`. You want to calculate a `fullName` from them by concatenating them. Moreover, you'd like `fullName` to update whenever `firstName` or `lastName` change. Your first instinct might be to add a `fullName` state variable and update it in and Effect:

```javascript
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

This is more complicated than necessary. It is inefficient too: it does an entire render pass with a stale value for `fullName`, them immediately re-renders with the updated value. Remove both the state variable and the Effect:

```javascript
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

When something can be calculated from the existing props or state, don't put it in state. Instead, calculate it during rendering. This makes your code faster (you avoid the extra "cascading" updates), simpler (you remove some code), and less error-prone (you avoid bugs caused by different state variables getting out of sync with each other). If this approach feels new to you, Thinking in React has some guidance on what should go into state.

## Caching expensive calculations

This component computes `visibleTodos` by taking the `todos` it receives by props and filtering them according to the `filter` prop. You might feel tempted to store the result in a state variable and update it in an Effect:

```javascript
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');

  // 🛑 Avoid: redundant state and unnecessary Effect
  const [visibleTodos, setVisibleTodos] = useState([]);
  useEffect(() => {
    setVisibleTodos(getFilteredTodos(todos, filter));
  }, [todos, filter]);;
}
```

Like in the earlier example, this both unnecessary and inefficient. First, remove the state and the Effect:

```javascript
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ This is fine if getFilteredTodos() is not slow
  const visibleTodos = getFilteredTodos(todos, filter);
  // ...
}
```

In many cases, this code is fine! But maybe `getFilteredTodos()` is slow or you have a lot of `todos`. In that case you don't want to recalculate `getFilteredTodos()` if some unrelated state variable like `newTodo` has changed.

You can cache (or "memoize") an expensive calculation by wrapping it in a `useMemo` Hook:

```javascript
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  const visibleTodos = useMemo(() => {
    // ✅ Does not re-run unless todos or filter change
    return getFilteredTodos(todos, filter);
  }, [todos, filter]);
  // ...
}
```

Or, written as a single line:

```javascript
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ Does not re-run getFilteredTodos() unless todos or filter change
  const visibleTodos = useMemo(() => getFilteredTodos(todos, filter), [todos, filter])
  // ...
}
```

This tells React that you don't want the inner function to re-run unless either `todos` or `filter` have changed. React will remember the return value of `getFilteredTodos()` during the initial render. During the next renders, it will check if `todos` or `filter` are different. If you're the same as last time, `useMemo` will return the last result it has stored. But if they are different, React will call the wrapped function again (and store the result instead).

The function you wrap in `useMemo` runs during rendering, so this only works for pure calculations.

> ##### Deep Dive
> #### How to tell if a calculation is expensive?
>
> In general, unless you're creating or looping over thousands of
> objects, it's probably not expensive. If you want to get some
> confidence, you can add a console log to measure the time spent in
> a piece of code:

```javascript
console.time('filter array');
const visibleTodos = getFilteredTodos(todos, filter);
console.timeEnd('filter array');
```

> Perform the interaction you're measuring (for example, typing into the
> input). You will then see logs for `filter array: 0.15ms` in your
> console. If the overall logged time adds up to a significant amount
> (say, `1ms` or more), it might make sense to memoize that calculation.
> As an experiment, you can then wrap the calculation in `useMemo` to
> verify whether the total logged time has decreased for that
> interaction or not:

```javascript
console.time('filter array');
const visibleTodos = useMemo(() => {
  return getFilteredTodos(todos, filter); // Skipped if todos and filter haven't changed
}, [todos, filter]);
console.timeEnd('filter array');
```

> `useMemo` won't make the first render faster. It only helps you skip
> unnecessary work on updates.
>
> Keep in mind that your machine is probably faster than your users' so
> it's a good idea to test the performance with an artificial slowdown.
> For example, Chrome offers a CPU Throttling option for this.
>
> Also note that measuring performance in development will not give you
> the most accurate results. (For example, when Strict Mode is on, you
> will see each component render twice rather than once.) To get the
> most accurate timings, build your app for production and text it on
> a device like your users have.

## Resetting all state when a prop changes

This `ProfilePage` component receives a `userId` prop. The page contains a comment input, and you use a `comment` state variable to hold its value. One day, you notice a problem: when you navigate from one profile to another, the `comment` state does not get reset. As a result, it's easy to accidentally post a comment on a wrong user's profile. To fix the issue, you want to clear out the `comment` state variable whenever the `userId` changes:

```javascript
export default function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');

  // 🛑 Avoid: Resetting state on prop change in an Effect
  useEffect(() => {
    setComment('');
  }, [userId]);
  // ..
}
```

This is inefficient because `ProfilePage` and its children will first render with the stale value, and then render again. It is also complicated because you'd need to do this in every component that has some state inside `ProfilePage`. For example, if the comment UI is nested, you'd want to clear out nested comment state too.

Instead, you can tell React that each user's profile is conceptually a different profile by giving it an explicit key. Split your component in two and pass a `key` attribute from the outer component to the inner one:

```javascript
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId}
    />
  );
}

function Profile({ userId }) {
  // ✅ This and any other state below will reset on key change automatically
  const [comment, setComment] = useState('');
  // ...
}
```

Normally, React preserves the state when the same component is rendered in the same spot. By passing `userId` as a `key` to the `Profile` component, you're asking React to treat two `Profile` components with different `userId` as two different components that should not share any state. Whenever the key (which you've set to `userId`) changes, React will recreate the DOM and reset the state of the `Profile` component and all of its children. As a result, the comment field will clear out automatically when navigating between profiles.

Note that in this example, only the outer `ProfilePage` component is exported and visible to other files in the project. Components rendering `ProfilePage` don't need to pass the key to it: they pass `userId` as a regular prop. The fact `ProfilePage` passes it as a `key` to the inner `Profile` component is an implementation detail.

## Adjusting some state when a prop changes

Sometimes, you might want to reset or adjust a part of the state of a prop change, but not all of it.

This `List` component receives a list of `items` as a prop, and maintains the selected item in the `selection` state variable. You want to reset the `selection` to `null` whenever the `items` prop receives a different array:

```javascript
functoni List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // 🛑 Avoid: Adjusting state on prop change in an Effect
  useEffect(() => {
    setSelection(null);
  }, [items]);
  // ...
}
```

This, too, is not ideal, Every time the `items` change, the `List` and its child components will render with a stale `selection` value at first. Then React will update the DOM and run the Effects. Finally, the `setSelection(null)` call will cause another re-render to the `List` and its child components, restarting this whole process again.

Start by deleting the Effect. Instead, adjust the state directly during rendering:

```javascript
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // Better: Adjust the state while rendering
  const [prevItems, setPrevItems] = useState(items);
  if (items !== prevItems) {
    setPrevItems(items);
    setSelection(null);
  }
  // ..
}
```

Storing information from previous renders like this can be hard to understand, but it's better than updating the same state in an Effect. In the above example, `setSelection` is called directly during a render. React will re-render the `List` immediately after it exits with a `return` statement. But that point, React hasn't rendered the `List` children or updated the DOM yet, so this lets the `List` children skip rendering the stale `selection` value.

When you update a component during rendering, React throws away the returned JSX and immediately retries rendering. To avoid very slow cascading retires, React only lets you update the same component's state during a render. If you update another component's state during a render, you'll see an error. A condition like `items !== prevItems` is necessary to avoid loops. You may adjust state like this, but any other side effects (like changing the DOM or setting a timeout) should remain in event handlers or Effects to keep you component predicable.

Although this pattern is more efficient than an Effect, most components shouldn't need it either. No matter how you do it, adjusting state based on props or other state makes your data flow more difficult to understand and debug. Always check whether you can reset all state with a key or calculate everything during rendering instead. For example, instead of storing (and resetting) the selected item, you can store the selected item ID:

```javascript
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selectedId, setSelectedId] = useState(null);
  // ✅ Best: Calculate everything during rendering
  const selection = items.find(item => item.id === selectedId) ?? null;
  // ...
}
```

Now there is not need to "adjust" the state at all. If the item with the selected ID is in the list, it remains selected. If it's not, the `selection` calculated during rendering will be `null` because no matching item was found. This behavior is a bit different, but arguably it's better because most changes to `items` now preserve the selection. However, you'd need to use `selection` in all the logic below because an item with `selectedId` might not exist.

## Sharing logic between event handlers

Let's say you have a product page with two buttons (but and Checkout) that both let you buy that product. You want to show a notification whenever the user puts the product in the cart. Adding the `showNotification()` call to both buttons' click handlers feel repetitive so you might be tempted to place this logic with an Effect:

```javascript
function ProductPage({ product, addToCart }) {
  // 🛑 Avoid: Event-specific logic inside an Effect
  useEffect(() => {
    if (product.isInCart) {
      showNotifications(`Added ${product.name} to the shopping cart!`);
    }
  }, [product]);

  function handleBuyClick() {
    addToCart(product);
  }

  function handleCheckoutClick() {
    addToCart(product);
    navigateTo('/checkout');
  }
  // ...
}
```

This Effect is unnecessary. It will also most likely cause bugs. For example, let's say that your app "remembers" the shopping cart between the page reloads. I you add a product to the cart once and refresh the page, the notification will appear again. It will keep appearing every time you refresh that product's page. This is because `product.isInCart` will already be `true` on the page load, so the Effect above will call `showNotifications()`.

When you're not sure whether some code should be in an Effect or in an event handler, ask yourself why this code needs to run. Use Effects only for code that should run because the component was displayed to the user. In this example, the notification should appear because the user pressed the button, bot because the page was displayed! Delete the Effect and put the shared logic into a function that you call from both event handlers:

```javascript
function ProductPage({ product, addToCart }) {
  // ✅ Good: Event-specific logic is called from event handlers
  function buyProduct() {
    addToCart(product);
    showNotification(`Added ${product.name} to the shopping cart!`);
  }

  function handleBuyClick() {
    buyProduct();
  }

  function handleCheckoutClick() {
    buyProduct();
    navigateTo('/checkout');
  }
  // ...
}
```

This both removes the unnecessary Effect and fixed the bug.

## Sending a POST request

This `Form` component sends two kinds of POST requests. It sends an analytics event when it mounts. When you fill in the form and click the Submit button, it will send a POST request to the `/api/register` endpoint:

```javascript
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic should run because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  // 🛑 Avoid: Event-specific logic inside an Effect
  const [jsonToSubmit, setJsonToSubmit] = useState(null);
  useEffect(() => {
    if (jsonToSubmit !== null) {
      post('/api/register', jsonToSubmit);
    }
  }, [jsonToSubmit]);

  function handleSubmit(e) {
    e.preventDefault();
    setJsonToSubmit({ firstName, lastName });
  }
  // ...
}
```

Let's apply the same criteria as in the example before.

The analytics POST request should remain in an Effect. This is because the reason to send the analytics event is that the form was displayed. (It would fire twice in development, but [see here](https://beta.reactjs.org/learn/synchronizing-with-effects#sending-analytics) for how to deal with that.)

However, the `/api/register` POST request is not caused by the form being displayed. You only want to send the request at one specific moment in time: when the user presses the button. It should only every happen on that particular interaction. Delete the second Effect and move that POST request into the event handler:

```javascript
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic runs because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  function handleSubmit(e) {
    e.preventDefault();
    // ✅ Good: Event-specific logic is in the event handler
    post('/api/register', { firstName, lastName });
  }
  // ...
}
```

When you choose whether to put some logic into an event handler or an Effect, the main question you need to answer is what kind of logic it is from the user's perspective. If this logic is caused by a particular interaction, keep it in the event handler. If it's caused by the user seeing the component on the screen, keep it in the Effect.

## Chains of computations

Sometimes you might feel tempted to chain Effects that each adjust a piece of state based on other state:

```javascript
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);
  const [isGameOver, setIsGameOver] = useState(false);

  // 🛑 Avoid: chains of Effects that adjust the state solely to trigger
  useEffect(() => {
    if (card !== null && card.gold) {
      setGoldCardCount(c => c + 1);
    }
  }, [card]);

  useEffect(() => {
    if (goldCardCount > 3) {
      setRound(r => r + 1);
      setGoldCardCount(0);
    }
  }, [goldCardCount]);

  useEffect(() => {
    if (rount > 5) {
      setIsGameOver(true);
    }
  }, [round]);

  useEffect(() => {
    alert('Good Game!');
  }, [isGameOver]);

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    } else {
      setCard(nextCard);
    }
  }

  //..
```

There are two problems with this code.

One problem is that it is very inefficient: the component (and its children) have to re-render between each `set` call in the chain. In the example above, in the worst case (`setCard` -> render -> `setGoldCardCount` -> render -> `setRound` -> render -> `setIsGameOver` -> render) there are three unnecessary re-renders of the tree below.

Even if it weren't slow, as your code evolves, you will run into cases where the "chain" you wrote doesn't fit the new requirements. Imagine you are adding a way to step through the history of the game moves. You'd do it by updating each state variable to a value from the past. However, setting the `card` state to a value from the past would trigger the Effect chain again and change the data you're showing. Code like this is often rigid and fragile.

In this case, it's better to calculate what you can during rendering, and adjust the state in the event handler:

```javascript
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);

  // ✅ Calculate what you can during rendering
  const isGameOver = round > 5;

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    }

    // ✅ Calculate all the next state in the event handler
    setCard(nextCard);
    if (nextCard.gold) {
      if (goldCardCount <= 3) {
        setGoldCardCount(goudCardCount + 1);
      } else {
        setGoldCardCount(0);
        setRount(round + 1);
        if (round === 5) {
          alert('Good game!');
        }
      }
    }
  }
  //...
```

This is a lot more efficient. Also, if you implement a way to view game history, now you will be able to set each state variable to move from the past without triggering the Effect chain that adjusts every other value. If you need to reuse logic between several event handlers, you can extract a function and call it from those handlers.

Remember that inside event handlers, state behaves like a snapshot. For example, even after you call `setRound(round + 1)`, the `round` variable will reflect the value at the time the user clicked the button. If you need to use the next value for calculations, define it manually like `const nextRound = round + 1`.

In some cases, you can't calculate the next state directly in the event handler. For example, imagine a form with multiple drop downs where the options of the next drop down depend on the selected value of the previous drop down. Then, a chain of Effects fetching data is appropriate because you are synchronizing with network.

## Initializing the application

Some logic should only run once when the app loads. You might place it in an Effect in the top-level component:

```javascript
function App() {
  // 🛑 Avoid: Effects with logic with should only every run once
  useEffect(() => {
    loadDataFromLocalStorage();
    checkAuthToken();
  }, []);
  // ...
}
```

However, you'll quickly discover that it runs twice in development. This can cause issues--for example, maybe it invalidates the authentication token because the function wasn't designed to be called twice. In general, your components should be resilient to being remounted. This includes your top-level `App` component. Although it may not every get remounted in practice in production, following the same constraints in all components makes it easier to move and reuse code. If some logic must run once per app load rather than once per component mount, you can add a top-level variable to track whether it has already executed, and always skip re-running it:

```javascript
let didInit = false;

function App() {
  useEffect(() => {
    if(!didInit) {
      didInit = true;
      // ✅ Only runs once per app load
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  }, []);
  // ...
}
```

You can also run it during module initialization and before the app renders:

```javascript
if (typeof window !== 'undefined') { // Check if we're running in the browser
  // ✅ Only runs once per all load
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

Code at the top level runs once when your component is imported--even if it doesn't end up being rendered. To avoid slowdown or surprising behavior when importing arbitrary components, don't overuse this pattern. Keep app-wide initialization logic to root component modules like `App.js` of in your application's entry point module.

## Notifying parent components about state changes

Let's say you're writing a `Toggle` component with an internal `isOn` state which can be either `true` or `false`. There are a few different ways to toggle it (by clicking or dragging). You want to notify the parent component whenever the `Toggle` internal state changes, so you expose an `onChange` event and call it from an Effect:

```javascript
function Toggle({ onchange }) {
  const [isOn, setIsOn] = useState(false);

  // 🛑 Avoid: The onChange handler runs too late
  useEffect(() => {
    onChange(isOn);
  }, [isOn, onChange]);

  function handleClick() {
    setIsOn(!isOn);
  }

  function handleDragEvent(e) {
    if (isCloserToRightEdge(e)) {
      setIsOn(true);
    } else {
      setIsOn(false);
    }
  }
  // ...
}
```

Like earlier, this is not ideal. The `Toggle` updates its state first, and React updates the screen. Then React runs the Effect, which calls the `onChange` function passed from a parent component. Now the parent component will update its own state, starting another render pass. It would be better to do everything in a single pass instead.

Delete the Effects and instead update the state of both components within the same event handler:

```javascript
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  function updateToggle(nextIsOn) {
    // ✅ Good: perform all updates during the event that caused them
    setIsOn(nextIsOn);
    onChange(nextIsOn);
  }

  function handleClick() {
    updateToggle(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      updateToggle(true);
    } else {
      updateToggle(false);
    }
  }
  // ...
}
```

With this approach, both the `Toggle` component and its parent component update their state during the event. React batches updates from different component together, so there will only be one render pass as a result.

You might also be able to remove the state altogether, and instead receive `isOn` from the parent component:

```javascript
:// ✅ Also good: the component is fully controlled by its parent
function Toggle({ isOn, onChange }) {
  function handleClick() {
    onChange(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      onChange(true);
    } else {
      onChange(false);
    }
  }
  // ...
}
```

"Lifting state up" lets the parent component fully control the `Toggle`
by toggling the parent's own state. This means the parent component will
have to contain more logic, but there will be less state overall to
worry about. Whenever you try to keep two different state
variables synchronized, it's a sign to try lifting state up
instead!

## Passing data to the parent

The `Child` component fetches some data and then passes it to the
`Parent` component in an Effect:

```javascript
function Parent() {
  const [data, setData] = useState(null);
  // ...
  return <Child onFetched={setData} />;
}

function Child({ onFetched }) {
  const data = useSomeAPI();
  // 🛑 Avoid: Passing data to parent in an Effect
  useEffect(() => {
    if (data) {
      onFetched(data);
    }
  }, [onFetched, data]);
  // ...
}
```

In React, data flows from the parent components to their children. When
you see something wrong on the screen, you can trace where the
information come from by going up the component chain until you find
which component passes the wrong prop or has the wrong state. When child
components update the state of their parent components in Effects, the
data flow becomes very difficult to trace. Since both the child and the
parent component need the same data, let the parent component fetch that
data, and pass it down to the child instead:

```javascript
function Parent() {
  const data = useSomeAPI();
  // ...
  // :checkmark: Good: Passing data down to the child
  return <Child data={data} />;
}

function Child({ data }) {
  // ...
}
```

This is simpler and keeps the data flow predictable: the data flows down
from the parent to the child.

## Subscribing to  an external store

Sometimes, your components need to subscribe to some data outside of the
React state. This data could be from a third-party library or a built-in
browser API. Since this data can change without React's knowledge, you
need to manually subscribe your components to it. This is often done
with an Effect, for example:

```javascript
function useOnlineStatus() {
  // Not ideal: Manual store subscription in an Effect
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function updateState() {
      setIsOnline(navigator.onLine);
    }

    updateState();

    window.addEventListener('online', updateState);
    window.addEventListener('offline', updateState);
    return () => {
      window.removeEventListener('online', updateState);
      window.removeEventListener('offline', updateState);
    };
  }, []);
  return isOnline;
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```
Here, the component subscribes to an external data store (in this case,
the browser `navigator.onLine` API). Since this API does not exist on
the server (so it can't be used to generate the initial HTML), initially
the state is set to `true`. When every the value of the data store
changes in the browser, the component updates its state.

Although it's common to use Effects for this, React has a purpose-built
Hook for subscribing to an external store that is preferred instead.
Delete the Effect and replace it with a call to `useSyncExternalStore`:

```javascript
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('inline', callback);
    window.removeEventListener('offline', callback);
  };
}

function useOnlineStatus() {
  // :checkmark: Good: Subscribing to an external store with a built-in
  Hook
  return useSyncExternalStore(
    subscribe, // React won't resubscribe for as long as you pass the same function
    () => navigator.onLine, // How to get the value of the client
    () => true // How to get the value of the server
  );
}

function ChatIndicator() {
  const isOnline = useOnlineStatue();
  // ...
}
```

This approach is less error-prone than manually syncing mutable data to
React state with an Effect. Typically, you'll write a custom Hook like
`useOnlineStatus()` above so that you don't need to repeat this code in
the individual components. Rea more about subscribing to external stores
from React components.

## Fetching data

Many apps use Effects to kick off data fetching. It is quite common to
write a data fetching Effect like this:

```javascript
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);

  useEffect(() => {
    // 🛑 Avoid: Fetchign without cleanup logic
    fetchResults(query, page).then(json => {
      setResults(json);
    });
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

You don't need to move this fetch to an event handler.

This might seem like a contradiction with the earlier examples where you
needed to put the logic into the event handlers! However, consider that
it's not the typing event that's the main reason to fetch. Search inputs
are often pre-populated from the URL, and the user might navigate Back
and Forward without touching the input. It doesn't matter where `page`
and `query` come from. while this component is visible, you want to keep
`results` synchronized with data from the network according to the
current `page` and `query`. This is why it's an Effect.

However, the code above has a bug. Imagine you type `"hello"` fast. Then
the `query` will change from `h`, to `he`, `hel`, `hell`, and `hello`.
This will kick off separate fetches, but there is no guarantee about
which order the responses will arrive in. For example, the `hell`
response may arrive after the `hello` response. Since it will call
`setResults()` last, you will be displaying the wrong search results.
This is called a `race condition`: two different request `raced` against
each other and come in a different order than you expected.

To fix the race condition, you need to add a cleanup function to ignore
stale responses:

```javascript
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);
  useEffect(() => {
    let ignore = false;
    fetchResults(query, page).then(json => {
      if(!ignore) setResults(json);
    });
    return () => {
      ignore = true;
    };
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

This ensures that when your Effect fetches data, all responses except
the last requested one will be ignored.

Handling race conditions is not the only difficulty with implementing
data fetching. You might also want to think about how to cache the
responses (so that the user can click Back and see the previous screen
instantly instead of a spinner), how to fetch them on the server (so
that the initial server-rendered HTML contains the fetched content
instead of a spinner), and how to avoid network waterfalls (so that a
child component that needs to fetch data doesn't have to wait for every
parent above it to finish fetching their data before it can start).
These issues apply to any UI library, not just React. Solving them is
not trivial, which is why modern frameworks provide more efficient
built-in data fetching mechanisms than writing Effect directly in your
components.

If you don't use a framework (and don't want to build your own) but
would like to make data fetching from Effects more ergonomic, consider
extracting your fetching logic into custom Hook like in this example:

```javascript
function SearchResults({ query }) {
  const [page, setPage] = useState(1);
  const params = new URLSearchParams({ query, page});
  const results = useData(`/api/search?${params}`);

  function handleNextPageClick() {
    setPage(page + 1);
  }
    // ...
}

function useData(url) {
  const [data, setData] useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then(response => response.json())
      .then(json => {
        if (!ignore) setData(json);
      });
    return () => {
      ignore = true;
    };
  }, [url]);
  return data;
}
```

You'll likely also want to add some logic for error handling and to
track whether the content is loading. You can build a Hook like this
yourself or use one of the many solutions already available in the React
ecosystem. Although this alone won't be as efficient as using a
framework's built-in data fetching mechanism, moving the data fetching
logic into a custom Hook will make it easier to adopt an efficient data
fetching strategy later.

In general, whenever you have to resort to writing Effects, keep an eye
out for when you can extract a piece of functionality into a custom
Hook with a more declarative and purpose-built API like `useData`
above. The fewer raw `useEffect` calls you have in your components, the
easier you will find to maintain your application.

## Recap

* If you can calculate something during render, you don't need an
  Effect.
* To cache expensive calculations, add `useMemo` instead of `useEffect`.
* To reset the state of an entire component tree, pass a different `key`
  to it.
* Code that needs to run because a component was displayed should be in
  Effects, the rest should be in events.
* If you need to update the state of several components, it's better to
  do it during a single event.
* Whenever you try to synchronize state variables in different
  components, consider lifting state up
* You can fetch data with Effect, but you need to implement cleanup to
  avoid race conditions.

# Challenges

## Challenge 1 of 4: Transform data without Effects

The `TodoList` below displays a list of todos. When the "Show only
active todos" checkbox is ticked, completed todos are not displayed in
the list. Regardless of which todos are visible, the footer displays teh
count to todos that are not yet completed.

Simplify this component by removing all the unnecessary state and
Effects.

`todos.js`:

```javascript
let nextId = 0;

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { initialTodos, createTodo } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [activeTodos, setActiveTodos] = useState([]);
  const [visibleTodos, setVisibleTodos] = useState([]);
  const [footer, setFooter] = useState(null);

  useEffect(() => {
    setActiveTodos(todos.filter(todo => !todo.completed));
  }, [todos]);

  useEffect(() => {
    setVisibleTodos(showActive ? activeTodos : todos);
  }, [showActive, todos, activeTodos]);

  useEffect(() => {
    setFooter(
      <footer>
        {activeTodos.length} todos left
      </footer>
    );
  }, [activeTodos]);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
      {footer}
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

## Challenge 2 of 4: Cache a calculation without Effects

In this example, filtering the todos was extracted into a separete
function called `getVisibleTodos()`. This function contians a
`console.log()` call inside of it which helps you notice when it's being
called. Toggle "Show only active todos" and notice that it causes
`getVisibleTodos()` to re-run. This is expected because visible todos
change when you toggle which ones to display.

Your task is to remove the Effect that recomputes the `visibleTodos`
list in the `TodoList` component. However, you need to make sure that
`getVisibleTodos()` does not re-run (and so does not print any logs)
when you type into the input.

`todos.js`:

```javascript
let nextId = 0;
let calls = 0;

export function getVisibleTodos(todos, showActive) {
  console.log(`getVisibleTodos() was called ${++calls} times`);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;
  return visibleTodos;
}

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

`App.js`:

```javascript
import { useState, useEffect } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [text, setText] = useState('');
  const [visibleTodos, setVisibleTodos] = useState([]);

  useEffect(() => {
    setVisibleTodos(getVisibleTodos(todos, showActive));
  }, [todos, showActive]);

  function handleAddClick() {
    setText('');
    setTodos([...todos, createTodo(text)]);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

## Challenge 3 of 4: Reset State without Effects

This `EditContact` component receives a contact object shaped like `{ id, name, email }` as the `savedContact` prop. Try editing the name and email input fields. When you press Save, the contact's buttons above the form updates to the edited name. When you press Reset, any pending changes in the form are discarded. Play around with this UI to get a feel for it.

When you select a contact with the buttons at the top, the form resets to reflects that contact's details. This is done with an Effect inside `EditContact.js`. Remove this Effect. Find another way to reset the form hen `savedContact.id` changes.

```javascript
import { useState, useEffect } from 'react';

export default function EditContact({ savedContact, onSave }) {
  const [name, setName] = useState(savedContact.name);
  const [email, setEmail] = useState(savedContact.email);

  useEffect(() => {
    setName(savedContact.name);
    setEmail(savedContact.email);
  }, [savedContact]);

  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: savedContact.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(savedContact.name);
        setEmail(savedContact.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

## Challenge 4 of 4:  Submit a form without Effects

This `Form` component lets you send a message to a friend. When you
submit the form, the `showForm` state variable is set to `false`. This
triggers an Effect calling `sendMessage(message)`, which sends the
message (you can see it in the console). After the message is sent, you
see a "Thank you" dialog with an "Open chat" button that lets you get
back to the form.

Your app's users are sending way too many messages. To make chatting a
little bit more difficult, you've decided to show the "Thank you" dialog
first rather than the form. change the `showForm` state variable to
initialize to `false` instead of `true`. As soon as you make that
change, the console will show that an empty message was sent. something
int his logic is wrong!

What's the root cause of this problem? And how can you fix it?

```javascript
import { useState, useEffect } from 'react';

export default function Form() {
  const [showForm, setShowForm] = useState(true);
  const [message, setMessage] = useState('');

  useEffect(() => {
    if (!showForm) {
      sendMessage(message);
    }
  }, [showForm, message]);

  function handleSubmit(e) {
    e.preventDefault();
    setShowForm(false);
  }

  if (!showForm) {
    return (
      <>
        <h1>Thanks for using our services!</h1>
        <button onClick={() => {
          setMessage('');
          setShowForm(true);
        }}>
          Open chat
        </button>
      </>
    );
  }

  return (
    <form onSubmit={handleSubmit}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit" disabled={message === ''}>
        Send
      </button>
    </form>
  );
}

function sendMessage(message) {
  console.log('Sending message: ' + message);
}
```

# Solutions

## Challenge 1 of 4:

There are only two essential pieces of state in this example: the list
of `todos` and the `showActive` state variable which represents whether
the checkbox is ticked. All of the other state variables are redundant
and can be calculated during rendering instead. This includes the
`footer` which you can move directly in to the surrounding JSX.

Your result should end up like this:

```javascript
import { useState } from 'react';
import { initialTodos, createTodo } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
      <footer>
        {activeTodos.length} todos left
      </footer>
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

## Challenge 2 of 4:

Remove the state variable and the Effect, and instead add a `useMemo` call to cache the result of calling `getVisibleTodos()`:

```javascript
import { useState, useMemo } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [text, setText] = useState('');
  const visibleTodos = useMemo(
    () => getVisibleTodos(todos, showActive),
    [todos, showActive]
  );

  function handleAddClick() {
    setText('');
    setTodos([...todos, createTodo(text)]);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

With this change, `getVisibleTodos()` will be called only if `todos` or `showActive` change. Typing into the input only changes the `text` state variable, so it does not trigger a call to `getVisibleTodos()`.

There is also another solution which does not need `useMemo`. Since the `text` state variable can't possibly affect the list of todos, you can extract the `NewTodo` form into a separate component, and move the `text` state variable inside of it:

```javascript
import { useState, useMemo } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const visibleTodos = getVisibleTodos(todos, showActive);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

This approach satisfies the requirements too. When you type into the input, only the `text` state variable updates. Since the `text` state variable is in the child `NewTodo` component, the parent `TodoList` component won't get re-rendered. This is why `getVisibleTodos()` doesn't get called when you type. (It would still be called if the `TodoList` re-renders for another reason.)

## Challenge 3 of 4:

Split the `EditContact` component in two. Move all the form state into
the inner `Editform` component. Export the outer `EditContact`
component, and make it pass `savedContact.id` as the `key` to the inner
`EditContact` component. As a result, the inner `EditForm` component
resets all of the form state and recreates the DOM whenever you select a
different contact.

```javascript
import { useState, useEffect } from 'react';

export default function EditContact({ ...props }) {
  return <EditForm {...props} key={props.savedContact.id} />
}

function EditForm({ savedContact, onSave }) {
  const [name, setName] = useState(savedContact.name);
  const [email, setEmail] = useState(savedContact.email);

  useEffect(() => {
    setName(savedContact.name);
    setEmail(savedContact.email);
  }, [savedContact]);

  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: savedContact.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(savedContact.name);
        setEmail(savedContact.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

## Challenge 4 of 4:

The `showForm` state variable determines whether to show the form or the
"Thank you" dialog. However, you aren't sending the message because the
"Thank you" dialog was displayed. You want to send the message because
the user has submitted the form. Delete the misleading Effect and move
the `sendMessage` call inside the `handleSubmit` event handler:

```javascript
import { useState, useEffect } from 'react';

export default function Form() {
  const [showForm, setShowForm] = useState(false);
  const [message, setMessage] = useState('');

  function handleSubmit(e) {
    e.preventDefault();
    setShowForm(false);
    sendMessage(message);
  }

  if (!showForm) {
    return (
      <>
        <h1>Thanks for using our services!</h1>
        <button onClick={() => {
          setMessage('');
          setShowForm(true);
        }}>
          Open chat
        </button>
      </>
    );
  }

  return (
    <form onSubmit={handleSubmit}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit" disabled={message === ''}>
        Send
      </button>
    </form>
  );
}

function sendMessage(message) {
  console.log('Sending message: ' + message);
}
```

Notice how in this version, only submitting the form (which is an event)
causes the message to be sent. It works equally well regardless of
whether `showForm` is initially set to `true` or `false`. (Set it to
`false` and notice no extra console messages.)



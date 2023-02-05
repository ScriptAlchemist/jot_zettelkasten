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

Note that in this example, only the outer `ProfilePage` component is exported and visible to other files int he project.


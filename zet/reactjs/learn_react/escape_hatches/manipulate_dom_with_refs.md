# Manipulating the DOM with Refs

React automatically updates to DOM to match your render output, so your component won't often need to manipulate it. However, sometimes you might need access to the DOM elements managed by React--for example, to focus a node, scroll to it, or measure its size and position. there is no built-in way to do those things in React, so you will need a ref to the DOM node.

> #### You will learn
>
> * How to access a DOM node managed by React with the `ref` attribute
> * How to `ref` JSX attribute relates to the `useRef` Hook
> * How to access another component's DOM node
> * In which cases it's safe to modify the DOM managed by React

## Getting a ref to the node

To access a DOM node managed by React, first, import the `useRef` Hook:

```javascript
import { useRef } from 'react';
```

Then, use it to declare a ref inside your component:

```javascript
const myRef = useRef(null);
```

Finally, pass it to the DOM node as the `ref` attribute:

```javascript
<div ref={myRef}>
```

The `useRef` Hook returns an object with a single property called `current`. Initially, `myRef.current` will be `null`. When React creates a DOM node for this `<div>`, React will put a reference to this node into `myRef.current`. You can then access this DOM node from your event handlers and use the built-in browser APIs defined on it.

```javascript
// You can use any browser APIs, for example:
myRef.current.scrollIntoVie();
```

### Example: Focusing a text input

In this example, clicking the button will focus the input:

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

To implement this:

1. Declare `inputRef` with the  `useRef` Hook.
2. Pass it as `<input ref={inputRef}>`. This tells React to put this `<input>`'s DOM node into `inputRef.current`.
3. In the `handleClick` function, read the input DOM node form `inputRef.current` and call `focus()` on it with `inputRef.current.focus()`.
4. Pass the `handleClick` event handler to `<button>` with `onClick`.

While DOM manipulation is the most common use case for refs, the `useRef` Hook can be used for storing other things outside React, like timer IDs. Similarly to state, refs remain between renders. Refs are like state variables that don't trigger re-renders when you set them. For an introduction to refs, see `Referencing Values with Refs`.

### Example: Scrolling to an element

You can have more than a single ref in a component. IN this example, there is a carousel of three images. Each button centers an image by calling the browser `scrollIntoView()` method the corresponding DOM node:

```javascript
import { useRef } from 'react';

export default function CatFriends() {
  const firstCatRef = useRef(null);
  const secondCatRef = useRef(null);
  const thirdCatRef = useRef(null);

  function handleScrollToFirstCat() {
    firstCatRef.current.scrollToView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center
    });
  }

  function handleScrollToSecondCat() {
    secondCatRef.current.scrollToView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center
    });
  }

  function handleScrollToThirdCat() {
    thirdCatRef.current.scrollToView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center
    });
  }

  return (
    <>
      <nav>
        <button onClick={handleScrollToFirstCat}>
          Tom
        </button>
        <button onClick={handleScrollToSecondCat}>
          Maru
        </button>
        <button onClick={handleScrollToThirdCat}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          <li>
            <img
              src="https://placekitten.com/g/200/200"
              alt="Tom"
              ref={firstCatRef}
            />
          </li>
          <li>
            <img
              src="https://placekitten.com/g/300/200"
              alt="Maru"
              ref={secondCatRef}
            />
          </li>
          <li>
            <img
              src="https://placekitten.com/g/250/250"
              alt="Jellyorum"
              ref={thirdCatRef}
            />
          </li>
        </ul>
      </div>
    </>
  );
}
```

> ##### Deep Dive
> #### How to manage a list of refs using a ref callback
>
> In the above examples, there is a predefined number of refs. However,
> sometimes you might need a ref to each item in the list, and you don't
> know how many you will have. Something like this wouldn't work:

```javascript
<ul>
  {items.map((item) => {
    // Doesn't work!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```

> this is because Hooks must only be called at the top-level of your
> component. You can't call `useRef` in a loop, in a condition, or
> inside a `map()` call.
>
> One possible way around this is to get a single ref to their parent
> element, and then use DOM manipulation methods like `querySelectorAll`
> to "find" the individual child nodes form it. However, this is brittle
> and can break if your DOM structure changes.
>
> Another solution is the pass a function to the `ref` attribute. This is
> called a `ref` callback. React will call your ref callback with the
> DOM node when it's time to set the ref, and with `null` when it's time
> to clear it. This lets you maintain your own array or a `Map`, and
> access any ref by its index or some kind of ID.
>
> This example shows how you can use this approach to scroll to an
> arbitrary node in a long list:

```javascript
import { useRef } from 'react';

export default function CatFriends() {
  const ItemsRef = useRef(null);

  function scrollToId(itemId) {
    const map = getMap();
    const node = map.get(itemId);
    node.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // Initialize the Map of first usage.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToId(0)}>
          Tom
        </button>
        <button onClick={() => scrollToId(5)}>
          Maru
        </button>
        <button onClick={() => scrollToId(9)}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          {calList.map(cat => (
            <li
              key={cat.id}
              ref={(node) => {
                const map = getMap();
                if (node) {
                  map.set(cat.id, node);
                } else {
                  map.delete(cat.id);
                }
              }}
            >
              <img
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: 1,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}
```

> In this example, `itemsRef` doesn't hold a single DOM node. Instead, it holds a `Map` from item ID to the DOM node. (`Refs can hold any values!`) The `ref` callback on every list item takes care to update the Map:

```javascript
<li
  key={cat.id}
  ref={node => {
    const map = getMap();
    if (node) {
      // Add to the Map
      map.set(cat.id, node);
    } else {
      // Remove from the Map
      map.delete(cat.id);
    }
  }}
>
```

> This lets you read individual DOM nodes from the Map later.

## Accessing another component's DOM nodes

When you put a ref on a built-in component that outputs a browser element like `<input />`, React will set the ref's `current` property to the corresponding DOM node (such as the actual `<input />` in the browser).

However, if you try to put a ref on your own component, like `<MyInput />`, by default you will get `null`. Here is an example demonstrating it. Notice how clicking the button does not focus the input:

```javascript
import { useRef } from 'react';

function MyInput(props) {
  return <input {...props} />;
}

export default function MyForm() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

To help you notice the issue, React also prints and error to the console:

```error
Warning: Function components cannot be given refs. Attempts to access this ref will fail. Did you mean to use React.forwardRef()?
```

This happens because by default React does not let a component access the DOM nodes of other components. Not even its own children! This is intentional. Refs are an escape hatch that should be used sparingly. Manually manipulating another component's DOM nodes make your code even more fragile.

Instead, components that want to expose their DOM nodes have to opt in the that behavior. A component can specify that it "forwards" its ref to one of its children. Here's how `MyInput` can use the `forwardRef` API:

```javascript
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```

This is how it works:

1. `<MyInput ref={inputRef} />` tells React to put the corresponding DOM node into `inputRef.current`. However, it's up to the `MyInput` component to opt into that--by default, it doesn't.
2. The `MyInput` component is declared using `forwardRef`. This opts it into receiving the `inputRef` from above as the second ref argument which is declared after `props`.
3. `MyInput` itself passes the `ref` it received to the `<input>` inside of it.

Now clicking the button to focus the input works:

```javascript
import { forwardRef, useRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
}

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

In design systems, it is a common pattern for low-level components like buttons, inputs, and so on, to forward their refs to their DOM nodes. ON the other hand, high-level components like forms, lists, or page sections usually won't expose their DOM nodes to avoid accidentally dependencies on the DOM structure.

> ##### Deep Dive
> #### Exposing a subset of the API with an imperative handle
>
> In the above example, `MyInput` exposes the original DOM input
> element. This lets the parent component call `focus()` on it. However,
> this also lets the parent component do something else--for example,
> change its CSS styles. In uncommon cases, you may want to restrict the
> exposed functionality. You can do that with `useImperativeHandle`:

```javascript
import {
  forwardRef,
  useRef,
  useImperativeHandle
} from 'react';

const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Only expose focus and nothing else
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input {...props} ref={realInputRef} />;
});

export default function form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

> Here, `realInputRef` inside `MyInput` holds the actual input DOM node.
> However, `useImperativeHandle` instructs React to provide your own
> special object as the value of a ref to the parent component. So
> `inputRef.current` inside the `Form` component will only have the
> `focus` method. In this case, the ref "handle" is not the DOM node,
> but the custom object you create inside `useImperativeHandle` call.

## When React attaches the refs

In React, every update is split in two phases:

* During render, React calls your components to figure out what should be on the screen.
* During commit, React applies changes to the DOM

In general, you don't want to access refs during rendering. That goes for refs holding DOM nodes as well. During the first render, the DOM nodes have not yet been created, so `ref.current` will be `null`. And during the rendering of updates, the DOM nodes haven't been updated yet. So it's too early to ready them.

React sets `ref.current` during the commit. Before updating the DOM, React sets the affected `ref.current` values to `null`. After updating the DOM, React immediately sets them to the corresponding DOM nodes.

Usually, you will access refs from event handlers. If you want to do something with a ref, but there is no particular event to do it in, you might need an Effect. We will discuss effects on the next pages.

> ##### Deep Dive
> #### Flushing state updates synchronously with `flushSync`
>
> Consider code like this, which adds a new todo and scrolls the screen
> down to the last child of the list. Notice how, for some reason, it
> always scrolls to the todo that was just before the last added one:

```javascript
import { useState, useRef } from 'react;

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(initialTodos);

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    setText('');
    setTodos([...todos, newTodo]);
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => {
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

> The issue is with these two lines:

```javascript
setTodos([...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```


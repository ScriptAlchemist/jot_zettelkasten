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

> In React, state updates are queued. Usually, this is what you want.
> However, here is causes a problem because `setTodos` does not
> immediately update the DOM. So the time you scroll the list to its
> last element, the todo has not yet been added. This is why scrolling
> always "lags behind" by one item.
>
> To fix this issue, you can force React to update ("flush") the DOM
> synchronously. To do this, import `flushSync` from `react-dom` and wrap
> the state update into `flushSync` call:

```javascript
flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

> This will instruct React to update the DOM synchronously right after
> the code wrapped in `flushSync` executes. As a result, the last todo
> will already be in the DOM by the time you try to scroll to it:

```javascript
import  useState, useRef } from 'react';
import { flusSync } from 'react-dom';

export default funciton TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodods] = useState(initialTodos);

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    flushSync(() => {
      setText('');
      setTodos([ ...todos, newTodo]);
    });
    listRef.current.lstChild.scrollIntiView({
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
        {todos.map(todo => (
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

## Best practices for DOM manipulation with Refs

Refs are an escape hatch. You should only use them when you have to "step outside React". Common examples of this include managing focus, scroll position, or calling browser APIs that React does not expose.

If you stick to non-destructive actions like focusing and scrolling, you shouldn't encounter any problems. However, if you try to modify the DOM manually, you can risk conflicting with the changes React is making.

To illustrate this problem, this example includes a welcome message and two buttons. The first button toggles its presence using `conditional rendering` and `state`,as you would usually do in React. The second button uses the `remove()` DOM API forcefully remove it from the DOM outside of React's control

Try pressing "Toggle with `setState`" a few times. The message should disappear and appear again. Then press "remove from the DOM". This will forcefully remove it. Finally, press "Toggle with `setState`":

```javascript
import {useState, useRef} from 'react';

export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {
          setShow(!show);
        }}>
        Toggle with setState
      </button>
      <button
        onClick={() => {
          ref.current.remove();
        }}>
        Remove from the DOM
      </button>
      {show && <p ref={ref}>Hello World</p>}
    </div>
  );
}
```

After you've manually removed the DOM element, trying to use `setState` to show it again will lead to a crash. This is because you've changed the DOM, and React doesn't know how to continue managing it correctly.

Avoid changing DOM nodes managed by React. Modifying, adding children to, or removing children from elements that are managed by React can lead to inconsistent visual results or crashes like above.

However, this doesn't mean that you can't do it at all. It requires caution. You can safely modify parts of the DOM that React has no reason to update. For example, if some `<div>` is always empty in the JSX, React won't have a reason to touch its children list. Therefore, it is safe to manually add or remove elements there.

## Recap

* Refs are a generic concept, but most often you'll use them to hold DOM elements.
* You instruct React to put a DOM node into `myRef.current` by passing `<div ref={myRef}>`.
* Usually, you will use refs for non-destructive actions like focusing, scrolling, or measuring DOM elements.
* A component doesn't expose its DOM nodes by default. You can opt into exposing a DOM node by using `forwardRef` and passing the second `ref` argument down to a specific node.
* Avoid changing DOM nodes managed by React.
* If you do modify DOM nodes managed by React, modify parts that React has no reason to update.

# Challenges

## Challenge 1 of 4: Play and pause the video

In this example, the button toggle a state variable to switch between a playing and paused state. However, in order to actually play or pause the video, toggling state is not enough. You also need to call `play()` and `pause()` on the DOM element for the `<video>`. Add the ref to it, and make the button work.

```javascript
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video width="250">
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

For an extra challenge, keep the "Play" button in sync with whether the video is playing even if the user right-clicks the video and plays it using the built-in browser media controls. You might want to listen to `onPlay` and `onPause` on the video to do that.

## Challenge 2 of 4: 

Make it so that clicking the "Search" button puts focus into the field.

```javascript
export default function Page() {
  return (
    <>
      <nav>
        <button>Search</button>
      </nav>
      <input
        placeholder="Looking for something?"
      />
    </>
  );
}
```

## Challenge 3 of 4: Scrolling an image carousel

This image carousel has a "Next" button that switches the active image. Mage the gallery scroll horizontally to the active image on click. You will want to call `scrollIntoView()` on the DOM node of the active image:

```javascript
node.scrollIntoView({
  behavior: 'smooth',
  block: 'nearest',
  inline: 'center'
});
```

```javascript
import { useState } from 'react';

export default function CatFriends() {
  const [index, setIndex] = useState(0);
  return (
    <>
      <nav>
        <button onClick={() => {
          if (index < catList.length - 1) {
            setIndex(index + 1);
          } else {
            setIndex(0);
          }
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li key={cat.id}>
              <img
                className={
                  index === i ?
                    'active' :
                    ''
                }
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
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}
```

## Challenge 4 of 4: Focus the search field with separate components

Make it so that clicking the "Search" button puts focus into the field. Note that each component is defined in a separate file and shouldn't be moved out of it. How do you connect them together?

`App.js`:

```javascript
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  return (
    <>
      <nav>
        <SearchButton />
      </nav>
      <SearchInput />
    </>
  );
}
```

`SearchButton.js`:

```javascript
export default function SearchButton() {
  return (
    <button>
      Search
    </button>
  );
}
```

`SearchInput.js`:

```javascript
export default function SearchInput() {
  return (
    <input
      placeholder="Looking for something?"
    />
  );
}
```

# Solutions

## Challenge 1 of 4:

Declare a ref and put it on the `<video>` element. Then call `ref.current.play()` and `ref.current.pause()` in the event handler depending on the state of the next state.

```javascript
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);
  const ref = useRef(null);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);

    if (nextIsPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video
        width="250"
        ref={ref}
        onPlay={() => setIsPlaying(true)}
        onPause={() => setIsPlaying(false)}
      >
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

In order to handle the built-in browser controls, you can add `onPlay` and `onPause` handlers to the `<video>` elements and call `setIsPlaying` from them. This way, if the user plays the video using the browser controls, the state will adjust accordingly.

## Challenge 2 of 4:

Add a ref to the input, and call `focus()` on the DOM node to focus it:

```javascript
import { useRef } from 'react';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <button onClick={() => {
          inputRef.current.focus();
        }}>
          Search
        </button>
      </nav>
      <input
        ref={inputRef}
        placeholder="Looking for something?"
      />
    </>
  );
}
```

## Challenge 3 of 4:

You can declare a `selectedRef` and then pass it conditionally only to the current image:

```javascript
<li ref={index === i ? selectRef : null}>
```

When `index === i`, meaning that the image is the selected one, the `<li>` will receive the `selectedRef`. React will make sure that `selectedRef.current` always points at the correct DOM node.

Note that the `flushSync` call necessary to force React to update the DOM before the scroll. Otherwise, `selectedRef.current` would always point at the previously selected item.

```javascript
import { useRef, useState } from 'react';
import { flushSync } from 'react-dom';

export default function CatFriends() {
  const selectedRef = useRef(null);
  const [index, setIndex] = useState(0);

  return (
    <>
      <nav>
        <button onClick={() => {
          flushSync(() => {
            if (index < catList.length - 1) {
              setIndex(index + 1);
            } else {
              setIndex(0);
            }
          });
          selectedRef.current.scrollIntoView({
            behavior: 'smooth',
            block: 'nearest',
            inline: 'center'
          });
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li
              key={cat.id}
              ref={index === i ?
                selectedRef :
                null
              }
            >
              <img
                className={
                  index === i ?
                    'active'
                    : ''
                }
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
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}
```

## Challenge 4 of 4:

You'll need to add an `onClick` prop to the `SearchButton`, and make the `SearchButton` pass it down to the browser `<button> `. You'll also pass a ref down to `<SearchInput>`, which will forward it to the real `<input>` and populate it. Finally, in the click handler, you'll call `focus` on the DOM node stored inside that ref.

`App.js`:

```javascript
import { useRef } from 'react';
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <SearchButton onClick={() => {
          inputRef.current.focus();
        }} />
      </nav>
      <SearchInput ref={inputRef} />
    </>
  );
}
```

`SearchButton.js`:

```javascript
export default function SearchButton({ onClick }) {
  return (
    <button onClick={onClick}>
      Search
    </button>
  );
}
```

`SearchInput.js`:

```javascript
import { forwardRef } from 'react';

export default forwardRef(
  function SearchInput(props, ref) {
    return (
      <input
        ref={ref}
        placeholder="Looking for something?"
      />
    );
  }
);
```



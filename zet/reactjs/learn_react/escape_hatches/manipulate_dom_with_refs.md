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

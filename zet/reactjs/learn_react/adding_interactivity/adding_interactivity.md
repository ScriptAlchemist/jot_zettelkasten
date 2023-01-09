# Adding Interactivity

Some things on the screen update in response to user input. for example,
clicking an image gallery switches the active image. In React, data that
changes over time is called state. You can add state to any component,
and update it as needed. In this chapter, you'll learn how to write
components that handle interactions, update their state, and display
different outputs over time.

> #### In this chapter
> * How to handle use-initiated events
> * How to make components "remember" information with state
> * How React updates the UI in two phases
> * Why state doesn't update right after you change it
> * How to queue multiple state updates
> * How to update an object in state
> * How to update an array in state

## Responding to events

React lets you add event handlers to your JSX. Event handlers are your
own functions that will be triggered in response to user interaction
like clicking, hovering, focusing on form inputs, and so on.

Built-in components like `<button>` only support built-in browser
events like `onClick`. However, you can also create your own
components, and give their event handler props any application-specific
names that you like.

```javascript
export default function App() {
  return (
    <Toolbar
      onPlayMovie={() => alert('Playing!')}
      onUploadImage={() => alert('Uploading!')}
    />
  );
}

function Toolbar({ onPlayMovie, onUploadImage }) {
  return (
    <div>
      <Button onClick={onPlayMovie}>
        Play Movie
      </Button>
      <Button onClick={onUploadImage}>
        Upload Image
      </Button>
    </div>
  );
}

function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

## State: a component's memory

Components often need to change what's on the screen as a result of an interaction. Typing into the form should update the input field, clicking "next" on an image carousel should change which image is displayed, clicking "but" puts a product in the shopping cart. Components need to "remember" things: the current input value, the current image, the shopping cart. In React, this kind of component-specific memory is called state.

You can add state to a component with a `useState` Hook. Hooks are special functions that let your components use React features (state is one of those features). The `useState` Hook lets you declare a state variable. It takes the initial state and returns a pair of values: the current state, and a state setter function that lets you update it.

```javascript
const [index, setIndex] = useState(0);
const [showMore, setShowMore] = useState(false);
```



# Basic Hooks

* useState
* useEffect
* useContext

## Additional Hooks

* useReducer
* useCallback
* useMemo
* useRef
* useImperativeHandle
* useLayoutEffect
* useDebugValue

## What is the point of hooks?

`State` can live in a class constructor

```javascript
class BtnOld extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <p>{this.state.count}</p>
    );
  }
}
```

It used to be tightly coupled to class components. Where it would almost be forced to use higher order components.

> anti-pattern

```javascript
function higherOrderCompoent(WrappedComponent) {
  return class extends React.Component {

    render() {
      return <WrappedCompoent { ...this.props} />;
    }
  }
}
```

This is too complex. So now comes in hooks.

* `Hooks` remove reactivity from components.
* We can call them `React Primatives` or `Building Blocks`

`useSuperPower();`

```javascript
function App() {

  useHook(); // Correct only call at the top of the functional component

  const fun = () => {
    useHook();// wrong can't do
  }

  return <button onClick={() => useHook() }></button>//wrong can't do
}
```

`Except` when used with `custom hooks`

### useState()











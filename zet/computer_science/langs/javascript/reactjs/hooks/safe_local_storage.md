# `useSafeLocalStorage`

A hook to set into `localstorage`:

## Full Hook

```javascript
import { useState } from 'react'

export function useSafeLocalStorage(key, initialValue) {
  const [valueProxy, setValueProxy] = useState(() => {
    try {
      const value = window.localStorage.getItem(key)
      return value ? JSON.parse(value) : initialValue
    } catch {
      return initialValue
    }
  })

  const setValue = value => {
    try {
      window.localStorage.setItem(key, value)
      setValueProxy(value)
    } catch {
      setValueProxy(value)
    }
  }

  return [valueProxy, setValue]
}
```

## The Breakdown

* The obvious imports: `import { useState } from 'react';`

* The basic function structure: `export function useSafeLocalStorage() {}`

* Setting values and value setter:

```javascript
const [valueProxy, setValueProxy] = useState(() => {
  try {
    const value = window.localStorage.getItem(key)
    return value ? JSON.parse(value) : initialValue
  } catch {
    return initialValue
  }
});
```

* This `useState` is a special one. There is a function inside of the
  initialization. Let's look at each piece.

  * Wrapping the function inside of a Try/Catch block: `try {} catch {}`

  * Inside of the `Try`. We have `value` becoming `window.localStorage.getItem(key)`

  * In our return function it checks if `value ` exists. If it does it will return `JSON.parse(value)` and if it is not. It will return `initialValue`

  * If it fails inside `Try` then we will return `initialValue` inside of the `Catch`.

  * Remember, we are just in the setting `valueProxy` value.

* The next few lines brings in another function called `setValue`:

```javascript
const setValue = value => {
  try {
    window.localStorage.setItem(key, value)
    setValueProxy(value)
  } catch {
    setValueProxy(value)
  }
}
```

* I want use to notice something before we go through the `setValue`
  function. When we return from the `useSafeLocalStorage` hook we return
  `valueProxy` (the `useState` value) and ` setValue`. I wanted to make
  this distinction. We will remind you about it later.

* Let's go into `setValue`. It takes in a `value` and attempts a
  `Try/Catch`.

  * Inside of the `Try`. We set local storage by `key` and set the `value` that was input into the `setValue` function:

    * `window.localStorage.setItem(key, value)`

    * Then we `setValueProxy` with `value` so our `useState` is updated.

  * Inside of the `Catch`. We will set `setValueProxy` with the value
    that was input into `setValue(value)`

* We return `[valueProxy, setValue]`

### What do we notice?

* When we initially start the hook component we initialize your value
  state. First looking for a `key` inside `localstorage`. If there is a
  `key`. The state value is the `value`

* This is wrapped in a `Try/Catch` block just in case `window` isn't
  there.

* The `setValue` function is setting or overriding the `localstorage`
  value at the `key`. It sets the hook state with the `value`. To be
  returned as `valueProxy`.

* The function to update it is returned as well: `setValue`

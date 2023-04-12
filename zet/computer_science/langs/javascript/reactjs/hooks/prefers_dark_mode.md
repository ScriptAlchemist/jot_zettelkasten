# `usePrefersDarkMode`

Let's start with the full hook and then break down each section.

## Full Hook

```javascript
import { useEffect, useState } from 'react';

export function usePrefersDarkMode() {
  const [value, setValue] = useState(true);

  useEffect(() => {
    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)')
    setValue(mediaQuery.matches)

    const handler = () => setValue(mediaQuery.matches)
    mediaQuery.addEventListeners('change', handler);
    return () => mediaQuery.removeEventListener('change', handler);
  }, [])

  return value;
}
```

## The Breakdown

* The obvious imports: `import { useEffect, useState } from 'react';`

* The basic function structure: `export function usePrefersDarkMode() {}`

* Setting values and value setter: `const [value, setValue] = useState(true);`

* We also have a `useEffect` included in this hook:

```javascript
useEffect(() => {
  const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
  setValues(mediaQuery.matches);

  const handler = () => setValue(mediaQuery.matches);
  mediaQuery.addEventListeners('change', handler);
  return () => mediaQuery.removeEventListener('change', handler);
}, []);
```

### What do we notice?

* Check into `window.matchMedia` for `'(prefers-color-scheme: dark)'`
  - Look in this because this would be the feature in browsers for the preferred color scheme on the browser. If it exists.

* Set the `setValues` with the `mediaQuery.matches` response.

* Create a handler `const handler = () => setValue(mediaQuery.matches);`

* Add and Event handler on `'change'` with the handler function:
  - `mediaQuery.addEventListeners('change', handler);`

* We always remove the event listeners we added:
  - `return () => mediaQuery.removeEventListener('change', handler);`
  - When this component is destroyed the `useEffect` will run this
    return function.

* `[]` no dependencies, but it does load once. Using no `[]` would force
  a re-render every time.

* Returns the `value`. It is auto set to `true`. Which would be `dark
  mode`

# `useDarkMode`

This hook's goal is the use and toggle Dark mode. Utilizing the `CSS
media feature` `prefers-color-scheme`. This should work with
`tailwindcss` and the `dark:text-white` style.

## Dependencies

* [usePrefersDarkMode](./prefers_dark_mode.md)
* [useSafeLocalStorage](./safe_local_storage.md)

## Full Hook

```javascript
import { useEffect } from 'react'
import { usePrefersDarkMode } from './usePrefersDarkMode'
import { useSafeLocalStorage } from './useSafeLocalStorage'

export function useDarkMode() {
  const [isEnabled, setIsEnabled] = useSafeLocalStorage('dark-mode', undefined)
  const prefersDarkMode = usePrefersDarkMode()

  const enabled = isEnabled === undefined ? prefersDarkMode : isEnabled

  useEffect(() => {
    if (window === undefined) return
    const root = window.document.documentElement
    root.classList.remove(enabled ? 'light' : 'dark')
    root.classList.add(enabled ? 'dark' : 'light')
  }, [enabled])

  return [enabled, setIsEnabled]
}
```

## The Breakdown

* The obvious imports: `import { useEffect } from 'react';`

* Import [usePrefersDarkMode](./prefers_dark_mode.md) and
  [useSafeLocalStorage](./safe_local_storage.md). The other two
  hooks included in this hook. A little hook threesome.

* The basic function structure: `export function useDarkMode() {}`

* Using `useSafeLocalStorage` we use `dark-mode` as `undefined`. This
  checks if there is a `value` at the `key` `dark-mode` inside of
  `localstorage`

  - `const [isEnabled, setIsEnabled] = useSafeLocalStorage('dark-mode', undefined)`

* Pulling in `usePrefersDarkMode` to check the `prefers-color-scheme`
  with `window.matchMedia('(prefers-color-scheme: dark)')`. If this is
  set. It will pull that value.

  - `const prefersDarkMode = usePrefersDarkMode()`

* If `isEnabled` is `undefined`. This means that there wasn't a `value`
  at the `key` in `localhost`. When you tried to check
  `useSafeLocalStorage('dark-mode', undefined)`.

  * If there is no `localstorage` value. It goes to `prefersDarkMode`. Or it stays as the `localstorage` value:

  - `const enabled = isEnabled === undefined ? prefersDarkMode : isEnabled`

* We also have a `useEffect` included in this hook:

```javascript
useEffect(() => {
  if (window === undefined) return
  const root = window.document.documentElement
  root.classList.remove(enabled ? 'light' : 'dark')
  root.classList.add(enabled ? 'dark' : 'light')
}, [enabled])
```

* Let's dig into this `useEffect`:

  * If there is a `window` we can continue on. If the `window` is `undefined` we `return`.

    - `if (window === undefined) return`

  * Pull out the `root` from `window.document.documentElement`:

    - `const root = window.document.documentElement;`

  * Remove from the `root.classList`. If `enabled` is `true`. The you remove `light`. If `enabled` is `false` remove `dark` from `window.document.documentElement.classList`

    - `root.classList.remove(enabled ? 'light' : 'dark')`

  * Add to the `root.classList`. If `enabled` is `true`. Add `dark` unless `enabled` is `false`. Then add `light` to the `window.document.documentElement.classList`

    - `root.classList.add(enabled ? 'dark' : 'light')`

  * If it removes `light` it adds `dark`. Or vice versa removes `dark`
    and adds `light`

  * Dependencies of the `useEffect` are `enabled`. So anytime the
    enabled changes value. `useEffect` should re-run.

    - `[enabled]`

* We return the `enabled` from `const enabled = isEnabled === undefined
  ? prefersDarkMode : isEnabled` and we also return `setIsEnabled`.
  Which is actually part of our `useSafeLocalStorage` hook. This makes
  the component re-render with the new `enabled` value.

  * `return [enabled, setIsEnabled]`

### What do we notice?

* There are lots of moving pieces in this hook. If you notice it is a
  collection of three. `usePrefersDarkMode` and `useSafeLocalStorage`.

* The `usePrefersDarkMode` is checking if there is a
  `prefers-color-scheme` on the `CSS media` feature.

* The `useSafeLocalStorage` is checking the `localstorage` for the `key`
  `dark-mode`. If it is in `localstorage`. It returns the `value`. If
  it's not there. It should return `undefined`.

* If it returns `undefined` then we use `prefersDarkMode`. Which will
  actually return `true` by default. So the webpage starts `dark` unless
  it's set in `localstorage` or set in `prefers-color-sheme`

* If there is not `window`. Nothing happens.

* We add and remove the calls on the `window.document.documentElement`
  and call it the `root`.

* We add `light` and remove `dark`. Or perform the opposite. This
  changes with the value of `enabled`.

* When `enabled` changes then you re-run the `useEffect`

* Returning the indicator `enabled` if the dark mode is turned on. Along
  with `setIsEnabled` to start the `useSafeLocalStorage` hook again. To
  go back down the line. Or set the default to dark.


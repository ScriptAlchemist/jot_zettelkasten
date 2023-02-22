# <Fragment> (<>...</>)

`<Fragment>`, often used via `<>...</>` syntax, lets you group elements
without a wrapper node.

```javascript
<>
  <OneChild />
  <AnotherChild />
</>
```

* [Reference](#reference)
  * [`<Fragment>`](#fragment)
* [Usage](#usage)
  * [Returning multiple elements](#returning-multiple-elements)
  * [Assigning multiple elements to a
    variable](#assigning-multiple-elements-to-a-variable)
  * [Grouping elements with text](#grouping-elements-with-text)
  * [Rendering a list of Fragments](#rendering-a-list-of-fragments)

---

## Reference

### `<Fragment>`

Wrap elements in `<Fragment>` to group them together in situations where
you need  a single element. Grouping elements in `Fragment` has no
effect on the resulting DOM; it is the same as if the elements were not
grouped. The empty JSX tag `<></>` is shorthand for
`<Fragment></Fragment>` in most cases.

#### Props

* options `key`: Fragments declared with the explicit `<Fragment>`
  syntax may have `keys`

#### Caveats

* If you want to pass `key` to a Fragment, you can't use the `<>...</>`
  syntax. You have to explicitly import `Fragment` from `'react'` and
  render `<Fragment key={yourKey}>...</Fragment>`.
* React does not reset state when you go from rendering `<><Child /></>`
  to `[<Child />]` or back, or when you go from rending `<><Child /></>`
  to `<Child />` and back. This only works a single level deep: for
  example, going from `<><><Child /></></>` to `<Child />` resets the
  state. See the precise semantics
  [here](https://gist.github.com/clemmy/b3ef00f9507909429d8aa0d3ee4f986b)

---

## Usage

### Returning multiple elements

Use `Fragment`, or the equivalent `<>...</>` syntax, to group multiple
elements together. You can use it to put multiple elements in any place
where a single element can go. For example, a component can only return
one element, but by using a Fragment you can group multiple elements
together and then return them as a group:

```javascript
function Post() {
  return (
    <>
      <Post title="An update" body="It's been a while since I posted..."
      />
      <Post title="My new blog" body="T am starting a new blog!" />
    </>
  )
}

function Post({title, body}) {
  return (
    <>

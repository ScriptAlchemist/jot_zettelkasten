# Passing Props to a Component

React components use props to communicate with each other. Every parent
component can pass some information to its child component by giving
them props. Props might remind you of HTML attributes, but you can pass
any JavaScript value through them, including objects, arrays, and
functions.

> #### You will learn
> * How to pass props to a component
> * How to read props from a component
> * How to specify default values for props
> * How to pass some JSX to a component
> * How props change over time

## Familiar props

Props are the information that you pass to a JSX tag. For example,
`className`, `src`, `alt`, `width`, and `height` are some of the props
you can pass to an `<img>`:

```javascript
function Avatar() {
  return (
    <img
      classname="avatar"
      src="https://i.imgur.com/1bX5QH6.jpg"
      alt="Lin Lanying"
      width={100}
      height={100}
    />
  );
}

export default function Profile() {
  return (
    <Avatar />
  );
}
```

The props you can pass to an `<img>` tag are predefined (ReactDOM conforms to the HTML standard). But you can pass any props to your own components, such as `<Avatar>`, to customize them. Here's how!

## Passing props to component

In this code, the `Profile` components isnt' passing any props to its child component, `Avatar`:

```javascript
export default function Profile() {
  return (
    <Avatar />
  );
}
```

You can give `Avatar` some props in two steps.

### Step 1: Pass props to the child component


First, pass some props to `Avatar`. For example, let's pass two props: `Person` (an object), and `size` (a number):

```javascript
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}
```

> If double curly braces after `person=` confuse you, remember they are
> merely an object inside the JSX curlies.

Now you can read these props inside the `Avatar` component.

### Step 2: REad props inside the child component

You can read these props by listing their names `person, size` separated by the commas inside `({` and `})` directly after `function Avatar`. This lets you use them inside the `Avatar` code, like you would with a variable.

```javascript
function Avatar({ person, size }) {
  // person and size are available here
}
```

Add some logic to `Avatar` that uses the `person` and `size` props for rendering, and you're done.

Now you can configure `Avatar` to render in many different ways with different props. Try tweaking the values!

`App.js`:

```javascript
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi', 
          imageId: 'YfeOqp2'
        }}
      />
      <Avatar
        size={80}
        person={{
          name: 'Aklilu Lemma', 
          imageId: 'OKS67lh'
        }}
      />
      <Avatar
        size={50}
        person={{ 
          name: 'Lin Lanying',
          imageId: '1bX5QH6'
        }}
      />
    </div>
  );
}
```

`utils.js`:

```javascript
export function getImageUrl(person, size = 's') {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

Props let you think about parent and child components independently. For example, you can change the `person` or the `size` props inside `Profile` without having to think about how `Avatar` uses them. Similarly, you can change how the `Avatar` uses these props, without looking at the `Profile`.

You can think of props like "knobs" that you can adjust. They serve the same role as arguments serve for functions--in fact, props are the only argument to your component! React component functions accept a single argument, a `props` object:

```javascript
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```

Usually you don't need the whole `props` object itself, so you destructure it into individual props.

> #### Pitfall
> Don't miss the pair of `{` and `}` curlies inside of `(` and `)` when
> declaring props:

```
function Avatar({ person, size }) {
  // ...
}
```

> This syntax is called "destructuring" and is equivalent to reading
> properties from a function parameter:

```
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ..
}
```

## Specifying a default value for a prop

If you want to give a prop a default value to fall back on when no value is specified, you can do it with the destructuring by putting `=` and the default value right after the parameter:

```javascript
function Avatar({ person, size = 100 }) {
  // ...
}
```

Now, if `<Avatar person={...} />` is rendered with no `size` prop, to `size` will be set to `100`.

The default value is only used if the `size` prop is missing or if you pass `size={undefined}`. But if you pass `size={null}` or `size={0}`, the default value will not be used.

## Forwarding props with the JSX spread syntax

Sometimes, passing props gets very repetitive:

```javascript
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}
```

There's nothing wrong with repetitive code--it can be more legible. But at times you may value conciseness. Some components forward all of their props to their children, like how this `Profile` does with `Avatar`. Because they don't use any of their props directly, it can make sense to use a more concise "spread" syntax:

```javascript
function Profile(props) {
  return (
    <div classname="card">
      <Avatar {...props} />
    </div>
  );
}
```

This forwards all of `Profie`'s props to the `Avatar` without listing each of their names.

Use spread syntax with restraint. If you're using it in every other component, something is wrong. Often, it indicates that you should split your components and pass children as JSX. More on that next!

## Passing JSX as children

It is common to nest built-in browser tags:

```
<div>
  <img />
</div>
```

Sometimes you'll want to nest your own components the same way:

```
<Card>
  <Avatar />
</Card>
```

When you nest content inside a JSX tag, the parent component will receive that content in a prop called `children`. For example, the `Card` component below will receive a `children` prop set to `<Avatar />` and render it in a wrapper div:

`App.js`:

```javascript
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}
```

`Avatar.js`:

```javascript
import { getImageUrl } from './utils.js;

export default function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}
```

`utils.js`:

```javascript
export function getImageUrl(person, size = 's') {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

Try replacing the `<avatar>` inside `<card>` with some text to see how the `Card` component can wrap any nested content. It doesn't need to "know" what's being rendered inside of it. You will see this flexible pattern in many places.

You can think of a component with a `children` prop as having a "hole" that can be "filled in" by its parent components with arbitrary JSX. You will often use the `children` prop for visual wrappers: panels, grids, and so on.

## How porps change over time

The `Clock` component below receives two props from its parent component: `color` and `time`. (The parent component's code is omitted because it uses `state`, which we won't dive into just yet.)

Try changing the color in the select box below:

```javascript
export default function Clock({ color, time }) {
  return ( 
    <h1 style={{ color: color }}>
      {time}
    </h1>
  );
}
```

This example illustrates that a component may receive different props over time. Props are not always static! Here, the `time` prop changes every second and the `color` prop changes when you select another color. Props reflect a component's data at any point in time, rather than only in the beginning.

However, props are `immutable`--a term from computer science meaning "unchangeable". When a component needs to change its props (for example, in response to a use interaction or new data), it will have to "ask" its parent component to pass it different props--a new object! Its old props will then be cast aside, and eventually the JavaScript engine will reclaim the memory taken by them.

Don't try to "change props". when you need to respond to the user input (like changing the selected color), you will need to "set state", which you can learn about in `State: A Component's Memory`.

## Recap

* To pass props, add them to the JSX, just like you would with HTML attributes.
* To read props, use the `function Avatar({ person, size })` destructuring syntax.
* You can specify a default value like `size = 100`, which is used for missing and `underfined` props.
* You can forward all props with `<Avatar {...props} />` JSX spread syntax, but don't overuse it!
* Nested JSX like `<Card><Avatar /></Card>` will appear as `Card` component's `children` prop.
* Props are read-only snapshots in time: every render receives a new version of props.
* You can't change props. When you need interactivity, you'll need to set state.

# Challenges

## Challenge 1 of 3: Extract a component

This `Gallery` component contains some very similar markup for two profile. Extract a `Profile` component out of it to reduce the duplication. You'll need to choose what props to pass to it.

`App.js`:

```javascript
import { getImageUrl } from './utils.js';

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <section className="profile">
        <h2>Maria Skłodowska-Curie</h2>
        <img
          className="avatar"
          src={getImageUrl('szV5sdG')}
          alt="Maria Skłodowska-Curie"
          width={70}
          height={70}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            physicist and chemist
          </li>
          <li>
            <b>Awards: 4 </b> 
            (Nobel Prize in Physics, Nobel Prize in Chemistry, Davy Medal, Matteucci Medal)
          </li>
          <li>
            <b>Discovered: </b>
            polonium (element)
          </li>
        </ul>
      </section>
      <section className="profile">
        <h2>Katsuko Saruhashi</h2>
        <img
          className="avatar"
          src={getImageUrl('YfeOqp2')}
          alt="Katsuko Saruhashi"
          width={70}
          height={70}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            geochemist
          </li>
          <li>
            <b>Awards: 2 </b> 
            (Miyake Prize for geochemistry, Tanaka Prize)
          </li>
          <li>
            <b>Discovered: </b>
            a method for measuring carbon dioxide in seawater
          </li>
        </ul>
      </section>
    </div>
  );
}
```

`Utils.js`:

```javascript
export function getImageUrl(imageId, size = 's') {
  return (
    'https://i.imgur.com/' +
    imageId +
    size +
    '.jpg'
  );
}
```

## Challenge 2 of 3: Adjust the image size based on a prop

In this example, `Avatar` receives a numeric `size` prop which determines the `<img>` width and height. The `size` prop is set to `40` in this example. However, if you open the image in a new tab, you'll notice that the image itself is larger (`160` pixels). The real image size is determined by which thumbnail size you're requesting.

Change the `Avatar` component to request the closest image size based on the `size` prop. Specifically, if the `size` is less than `90`, pass `s` ("small") rather than `b` ("big") to the `getImageUrl` function. Verifying that your changes work by rendering avatars with different values of the `size` prop and opening images in a new tab.

`App.js`:

```javascript
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person, 'b')}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <Avatar
      size={40}
      person={{ 
        name: 'Gregorio Y. Zara', 
        imageId: '7vQD0fP'
      }}
    />
  );
}
```

`Utils.js`:

```javascript
export function getImageUrl(person, size) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

## Challenge 3 of 3: Passing JSX in a `children` prop

Extract a `Card` component from the markup below, and use the `children` prop to pass different JSX to it:

```javascript
export default function Profile() {
  return (
    <div>
      <div className="card">
        <div className="card-content">
          <h1>Photo</h1>
          <img
            className="avatar"
            src="https://i.imgur.com/OKS67lhm.jpg"
            alt="Aklilu Lemma"
            width={70}
            height={70}
          />
        </div>
      </div>
      <div className="card">
        <div className="card-content">
          <h1>About</h1>
          <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
        </div>
      </div>
    </div>
  );
}
```

## Solutions

### 1 of 3

`App.js`:

```javascript
import { getImageUrl } from './utils.js';

function Profile({ 
                  name, 
                  src, 
                  alt, 
                  width, 
                  height, 
                  profession, 
                  discovered,
                  awards }) {
  return (
    <section className="profile">
        <h2>{name}</h2>
        <img
          className="avatar"
          src={src}
          alt={name}
          width={width}
          height={height}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            {profession}
          </li>
          <li>
            <b>Awards: {awards.length} </b> 
            ({awards.join(', ')})
          </li>
          <li>
            <b>Discovered: </b>
            {discovered}
          </li>
        </ul>
      </section>
  )
}
export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <Profile
        name="Maria Skłodowska-Curie"
        src={getImageUrl('szV5sdG')}
        width={70}
        height={70}
        profession="physicist and chemist"
        discovered="polonium (element)"
        awards={[
          "Nobel Prize in Physics",
          "Nobel Prize in Chemistry",
          "Davy Medal",
          "Matteucci Medal"
        ]}
      />
      <Profile
        name="Katsuko Saruhashi"
        src={getImageUrl('YfeOqp2')}
        width={70}
        height={70}
        profession="geochemist"
        discovered="a method for measuring carbon dioxide in seawater"
        awards={[
          "Miyake Prize for geochemistry",
          "Tanaka Prize"
        ]}
      />
            
    </div>
  );
}
```

### 2 of 3

`App.js`:

```javascript
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  const imageSize = (size < 90) ?'s':'b';
  return (
    <img
      className="avatar"
      src={getImageUrl(person, imageSize)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <>
      <Avatar
        size={40}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
      <Avatar
        size={100}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
    </>
  );
}
```

### 3 of 3

`App.js`:

```javascript
function Card({ children, title }) {
  return (
    <div className="card">
        <div className="card-content">
          <h1>{title}</h1>
          {children}
        </div>
    </div>
  );
}

export default function Profile() {
  return (
    <div>
      <Card title="Photo">
          <img
            className="avatar"
            src="https://i.imgur.com/OKS67lhm.jpg"
            alt="Aklilu Lemma"
            width={70}
            height={70}
          />
      </Card>
      <Card title="About">
          <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
      </Card>
    </div>
  );
}
```





























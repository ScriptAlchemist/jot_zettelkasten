# Quick Start

Welcome to the React documentation! This page will give you an
introduction to the 80% of React concepts that you will use on a daily
basis.

#### You will learn

* How to create and nest components
* How to add markup and styles
* How to display data
* How to render conditions and lists
* How to respond to events and update the screen
* How to share data between components

### Creating and nesting components

React apps are made out of `components`. A component is a piece of the
UI (user interface) that has its own logic and appearance. A component
can be as small as a button, or as large as an entire page.

React components are JavaScript functions that return markup:

```javascript
function MyButton() {
  return (
    <button>I'm a button</button>
  );
}
```

Now that you've declared `MyButton`, you can nest it into another component:

```javascript
export default function MyApp() {
  return (
   <div>
     <h1>Welcome to my app</h1>
     <MyButton />
   </div>
  );
}
```

Notice that `<MyButton />` starts with a capital letter. That's how you know it's a React component. React component names must always star with a capital letter, while HTML tags must be lowercase.

[Have a look at the result](https://codesandbox.io/s/ieh8uf?file=%2FApp.js&utm_medium=sandpack)

The `export default` keyboards specify the main component in the file. If you're not familiar with some piece of JavaScript syntax, `MDN` and `javascript.info` have great references.

### Writing markup with JSX

The markup syntax you've seen above is called `JSX`. It is optional, but most React projects use JSX for its convenience. All of the tools we recommend for local development support JSX out of the box.

JSX is stricter than HTML. You have to close tags like `<br />`. Your component also can't return multiple JSX tags. You have to wrap them into a shared parent, like a `<div>...</div>` or an empty `<>...</>` wrapper:

```javascript
function AboutPage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  );
}
```

If you have a lot of HTML to port to JSX, you can use an online converter.

### Adding stlyes

In React, you specify a CSS class with `className`. It works the same way as the HTML `class` attribute:

```javascript
<img className="avatar" />
```

Then you write the CSS rules for it in a separate CSS file:

```css
/* In your CSS */
.avatar {
  border-radius: 50%;
}
```

React does not prescribe how you add CSS files. In the simplest case, you'll add a `<link>` tag to your HTML. If you use a build or a framework, consult its documentation to learn how to add CSS file to your project.

### Displaying data

JSX lets you put markup into JavaScript. Curly braces let you "escape back" into JavaScript so that you can embed some variable for your code and display it to the user. For example, this will display `user.name`:

```javascript
return (
  <h1>
    {user.name}
  </h1>
);
```

You can also "escape into JavaScript" from JSX attributes, but you have to use curly braces instead of quotes. For example, `className="avatar"` passes the `"avatar"` string as the CSS class, but `src={urse.imageUrl}` reads the JavaScript `user.imageUrl` variable value, and then passes that value as the `src` attribute:

```javascript
return (
  <img
    className="avatar"
    src={user.imageUrl}
  />
);
```

You can put more complex expressions inside the JSX curly braces too, for example, [string concatenation](https://codesandbox.io/s/15oriv?file=%2FApp.js&utm_medium=sandpack):

```javascript
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```

In the above example, `style={{}}` is not a special syntax, but a regular `{}` object inside the `style={ }` JSX curly braces. You can use the `style` attribute when your styles depend on JavaScript variables.

### Conditional Rendering

In React, there is no special syntax for writing conditions. Instead,
you'll use the same techniques as you use when writing regular
JavaScript code. For example, you can use an `if` statement to
conditionally include JSX:

```javascript
let content;
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  constent = <LoginForm />;
}

return (
  <div>
    {content}
  </div>
);
```

If you prefer more compact code, you can use the `conditional ?
operator`. Unlike `if`, it works inside JSX;

```javascript
<div>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```

When you don't need the `else` branch, you can also use a shorter
`logical && syntax`:

```javascript
<div>
  {isLoggedIn && <AdminPanel />}
</div>
```

All of these approaches also work for conditionally specifying attributes. If you're unfamiliar with some of this JavaScript syntax, you can start by always using `if...else`.

### Rendering Lists

You will rely on JavaScript features like `for` loop and the array `map()` function to render lists of components.

For example, lets' say you have an array of products:

```javascript
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apples', id: 3 },
];
```

Inside your component, use the `map()` function to transform an array of products into an array of `<li>` items:

```javascript
const listItems = products.map(products =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

Notice how `<li>` has a `key` attribute. For each item in a list, you should pass a string or a number that uniquely identifies that item among its siblings. Usually, a key should be coming from your data, such as a database ID. React will rely on your keys to understand what happened if you later insert, delete, or reorder the items.






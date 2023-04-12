# JavaScript in JSX with Curly Braces

JSC lets you write HTML-like markup inside a JavaScript file, keeping
rendering logic and content in the same place. Sometimes you will want
to add a little JavaScript logic or reference a dynamic property inside
that markup. In this situation, you can use curly braces in your JSX to
open a window to JavaScript.

> ### You will learn
>
> * How to pass trings with quotes
> * How to reference a JavaScript variable inside JSX with curly braces
> * How to call a JavaScript function inside JSX with curly braces
> * How to use a JavaScript object inside JSX with curly braces
>

## Passing strings with quotes

When you want to pass a string attributes to JSX, you put it in single or double quotes:

```javascript
export default function Avatar() {
  return (
    <img
      className="avatar"
      src="https://i.imgur.com/7vQD0fPs.jpg"
      alt="Gregorio Y. Zara"
    />
  );
}
```

Here, `https://i.imgur.com/7vQD0fPs.jpg` and `Gregorio Y. Zara` are being passed as strings.

But what if you want to dynamically specify the `src` or `alt` text? You could use a value from JavaScript by replacing `"` and `"` with `{` and `}`:

```javascript
export default function Avatar() {
  const avatar = 'https://i.imgur.com/7vQD0fPs.jpg';
  const description = 'Gregorio Y. Zara';
  return (
    <img
      className="avatar"
      src={avatar}
      alt={description}
    />
  );
}
```

Notice the difference between `className="avatar"`, which specifies an `"avatar"` CSS class name that makes the image round, and `src={avatar}`. That's because curly braces let you work with JavaScript right there in your markup!

## Using curly braces: A window into the JavaScript world

JSX is a special way of writing JavaScript. That means it's possible to use JavaScript inside it--with curly braces `{ }`. The example below first declares a name for the scientist, `nmae`, then embeds it with curly braces inside the `<h1>`.

```javascript
export default function TodoList() {
  const name = "Gregorio Y. Zara';
  return (
    <h1>{names}'s To Do List</h1>
  );
}
```

Try changing `names`'s value from `Gregorio Y. Zara` to `Hedy Lamarr`. See how the To To List title changes?

Any JavaScript expression will work between curly braces, including function calls like `formatDate()`:

```javascript
const today = new Date();

function formatDate(date) {
  return new Intl.DateTimeFormat(
    'en-US',
    { weekday: 'long' }
  ).format(date);
}

export default function TodoList() {
  return (
    <h1>To Do List for {formatDate(today)}</h1>
  );
}
```

### Where to use curly braces

You can only use curly braces in two ways inside JSX:

1. `As text` directly inside a JSX tag: `<h1>{name}'s To Do List</h1>` works, but `<{tag}>Gregoria Y. Zara's To Do List</{tag}>` will not.
2. `As attributes` immediately following the `=` sign: `src={avatar}` will read the `avatar` variable, but `src="{avatar}"` will pass a string `"{avatar}"`.

## Using "double curlies": CSS and other objects in JSX

In addition to strings, numbers, and other JavaScript expressions, you can even pass objects in JSX. Objects are also denoted with curly braces, like `{ name: "Hedy Lamarr", inventions: 5 }`. Therefore, to pass a JS object in JSX, you must wrap the object in another pair of curly braces: `person={{ name: "Hedy Lamarr", inventions: 5 }}`.

You may see this with inline CSS styles in JSX. React does not require you to use inline styles (CSS classes work great for the most cases). But when you need an inline style, you pass an object to the `style` attribute:

```javascript
export default function TodoList() {
  return (
    <ul style={{
      backgroundColor: 'black',
      color: 'pink'
    }}>
      <li>Imporve the videophone</li>
      <li>Prepare aeronatutics lectures</li>
      <li>Woek on the alcohol-fuelled engine</li>
    </ul>
  );
}
```

Try changing the values of `backgroundColor` and `color`.

You can really see the JavaScript object inside the curly braces when you write it like this:

```javascript
<ul style={
  {
    backgroundColor: 'black',
    color: 'pink'
  }
}>
```

The next time you see `{{` and `}}` in JSX,  knows that it's nothing more than and object inside the JSX curlies!

> #### Pitfall
>
> Inline `style` properties are written in camelCase. For example, HTML
> `<ul style="background-color: black">` would be written as `<ul
> style={{ backgroundColor: 'black' }}>` in your component.

### More fun with JavaScript obejcts and curly braces

You can move several expressions into one objects, and reference them in your JSX inside curly braces:

```javascript
const prtson = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work n the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

In this example, the `person` JavaScript object contains a `name` string and a `theme` object:

```javascript
const person = {
  name: "Gregoria Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};
```

The components can use these values from `person` like so:

```javascript
<div style={person.theme}>
  <h1>{person.name}'s Todos</h1>
```

JSX is very minimal as a templating language because it lets you organize data and logic using JavaScript.

## Recap

Now you know almost every thing about JSX:

* JSX attributes inside quotes are passed as strings.
* Curly braces let you bring JavaScript logic and variables into your markup.
* They work inside the JSX tag content or immediately after `=` in attributes.
* `{{` and `}}` is not special syntax: it's a JavaScript object tucked inside JSX curly braces.

## Challenges

### Challenge 1 of 3: Fix the mistake

This code crashes with an error saying `Objects are not valid as React child`:

```javascript
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

### Challenge 2 of 3: Extract information into an object 

Extract the image URL into the person object.

```javascript
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

### Challenge 3 of 3: Write an expression inside JSX curly braces 

In the object below, the full image URL is split into four parts: base URL, `imageId`, `imageSize`, and file extension.

We want the image URL to combine these attribute together: base URL (always `https://i.imgur.com/`), `imageId` (`7vQD0fP`), `imageSize` (`s`), and file extension (always `.jpg`). However, something is wrong with how the `<img>` tag specifies its `src`.

Can you fix it?

```javascript
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

#### Solutions

#### Challenge 1

```javascript
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

#### Challenge 2

```javascript
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  },
  image: {
    src: "https://i.imgur.com/7vQD0fPs.jpg",
    alt: "Gregorio Y. Zara"
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={person.image.src}
        alt={person.image.alt}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

#### Challenge 3

```javascript

const baseUrl = 'https://i.imgur.com/';
const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={baseUrl + person.imageId + person.imageSize + '.jpg'}
        alt={person.name}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

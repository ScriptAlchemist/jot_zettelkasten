# Reacting to Input with State

React uses a declarative way to manipulate the UI. Instead of manipulating individual pieces of the UI directly, you describe the different states that your component can be in, and switch between them in response to the user input. This is similar to how designers think about the UI.

> #### You will learn
>
> * How declarative UI programming differs from imperative UI programming
> * How to enumerate the different visual states your component can be in
> * How to trigger the changes between the different visual state from code

## How declarative UI compares to imperative

When you design UI interactions, you probably think about how the UI changes in response to user actions. Consider a form that lets the user submit an answer:

* When you type something into a form, the "Submit" button becomes enabled.
* When you press "Submit", both form and the button become disabled, and a spinner appears.
* If the network request succeeds, the form gets hidden, and the "Thank You" message appears.
* If the network request fails, an error message appears, and the form becomes enabled again.

In imperative programming, the above corresponds directly to how you implement interaction. You have to write the exact instructions to manipulate the UI depending on what just happened. Here's another way to think about this: imagine riding next to someone in a car and telling them turn by turn where to go.

They don't know where you want to go, they just follow your commands. (And if you get the directions wrong, you end up in the wrong place!) It's called imperative because you have to "command" each element, from the spinner to the button, telling the computer how to update the UI.

In this example of imperative UI programming, the form is built without React. It uses the built-in browser DOM:

`index.html`:

```html
<form id="form">
  <h2>City quiz</h2>
  <p>
    What city is located on two continents?
  </p>
  <textarea id="textarea"></textarea>
  <br />
  <button id="button" disabled>Submit</button>
  <p id="loading" style="display: none">Loading...</p>
  <p id="error" style="display: none; color: red;"></p>
</form>
<h1 id="success" style="display: none">That's right!</h1>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
</style>
```

`index.js`:

```javascript
async function handleFormSubmit(e) {
  e.preventDefault();
  disable(textarea);
  disable(button);
  show(loadingMessage);
  hide(errorMessage);
  try {
    await submitForm(textarea.value);
    show(successMessage);
    hide(form)
  } catch (err) {
    show(errorMessage);
    errorMessage.textContent = err.message;
  } finally {
    hide(loadingMessage);
    enable(textarea)
    enable(button);
  }
}

function handleTextareaChange() {
  if (textarea.value.length === 0) {
    disable(button);
  } else {
    enable(button);
  }
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

function enable(el) {
  el.disabled = false;
}

function disable(el) {
  el.disabled = true;
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (answer.toLowerCase() === 'istanbul') {
        resolve();
      } else {
        reject(new Error('Good guess by a wrong answer. Try again!'));
      }
    }, 1500);
  });
}

let form = document.getElementById('form');
let textarea = document.getElementById('textarea');
let button = document.getElementById('button');
let loadingMessage = document.getElementById('loading');
let errorMessage = document.getElementById('error');
let successMessage = document.getElementById('success');
form.onsubmit = handleFormSubmit;
textarea.oninput = handleTextareaChange;

```

Manipulating the UI imperatively works well enough for isolated examples, but it gets exponentially more difficult to manage in more complex systems. Imagine updating a page full of different forms like this one. Adding a new UI element or a new interaction would require carefully checking all existing code to make sure you haven't introduced a bug (for example, forgetting to show or hide something).

React was built to solve this problem.

In React, you don't directly manipulate the UI--meaning you don't enable, disabled, show, or hide components directly. Instead, you declare what you want to show, and React figures out how to update the UI. Think of getting into a taxi and telling the driver where you want to go instead of telling them exactly where to turn. It's the driver's job to get you there, and they might even know some shortcuts you haven't considered!

## Thinking about UI declaratively

You've seen how to implement a form imperatively above. To better understand how to think in React, you'll walk through reimplementing this UI in React below:

1. Identify your component's different visual states
2. Determine what triggers those state changes
3. Represent the state in memory using `useState`
4. Remove any non-essential state variables
5. Connect the event handlers to set the state

### Step 1: Identify your component's different visual states

In computer science, you may hear about a "state machine" being in one of several "states". If you work with a designer, you may have seen mockups for different "visual states". React stands at the intersection of design and computer science, so both of these ideas are sources of inspiration.

First, you need to visualize all the different "states" of the UI the user might see:

* `Empty`: Form has a disabled "Submit" button
* `Typing`: Form has an enabled "Submit" button
* `Submitting`: Form is completely disabled. Spinner is shown
* `Success`: "Thank You" message is shown instead of a form
* `Error`: Same as Typing state, but with an extra error message.

Just like a designer, you'll want to "mock up" or create "mocks" for different states before you add logic. For example, here is a mock for just the visual part of the form. This mock is controlled by a prop called `status` with a default value of `empty`:

```javascript
export default function Form({
  status = 'empty'
}) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form>
        <textarea />
        <br />
        <button>
          Submit
        </button>
      </form>
    </>
  );
}
```

You could call that prop anything you like, the naming is not important. Try editing `status = 'empty'` to `status = 'success'` to see the success message appear. Mocking lets you quickly iterate on the UI before you write up any logic. Here is a more fleshed out prototype of the same component, still "controlled" by the `status` prop:

```javascript
export default function Form({
  // Try 'submitting', 'error', 'success':
  status = 'empty'
}) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form>
        <textarea disabled={
          status === 'submitting'
        } />
        <br />
        <button disabled={
          status === 'empty' ||
          status === 'submitting'
        }>
          Submit
        </button>
        {status === 'error' &&
          <p className="Error'>
            Good guess but a wrong answer. Try again!
          </p>
        }
      </form>
    </>
  );
}
```

> ##### Deep Dive
>
> If a component has a lot of visual states, it can be convenient to
> show them all on one page:

`App.js`:

```javascript
import Form from './Form.js';

let statuses = [
  'empty',
  'typing',
  'submitting',
  'success',
  'error',
];

export default function App() {
  return (
    <>
      {statuses.map(status => (
        <section key={status}>
          <h4>Form ({status}):</h4>
          <Form status={status} />
        </section>
      ))}
    </>
  );
}
```

`Form.js`:

```javascript
export default function Form({ status }) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <form>
      <textarea disabled={
        status === 'submitting'
      } />
      <br />
      <button disabled={
        status === 'empty' ||
        status === 'submitting'
      }>
        Submit
      </button>
      {status === 'error' &&
        <p className="Error">
          Good guess but a wrong answer. Try again!
        </p>
      }
    </form>
  );
}
```

> Pages like this are often called "living style guides" or "storybooks".

### Step 2: Determine what triggers those state changes

You can trigger state updates in response to two kinds of inputs:

* Human inputs, like clicking a button, typing in a field, navigating a link.
* Computer inputs, like a network response arriving, a timeout
  completing, an image loading.

In both cases, you must set state variables to update the UI. For the form you're developing, you will need to change state in response to a few different inputs:

* `Changing the text input` (human) should switch it from the Empty state to the Typing state of back, depending on whether the text box is empty or not.
* `Clicking the Submit button` (human) should switch it to the Submitting state.
* `Successful network response` (computer) should switch it to the Success state
* `Failed network response` (computer) should switch it to the Error state with the matching error message.

> Notice that human inputs often require `event handlers`.

To help visualize this from, try drawing each state on paper as a labeled circle, and each change between two states as an arrow. You can sketch out many flows this way and sort out bugs long before implementation.

```

[empty] -start-typing-> [typing]-press-submit->[submitting]
                                               /       \
                                          Network      Network
                                          Error        Success
                                          /               \
                                         v                 v 
                                      [Error]            [Success]
```

### Step 3: Represent the state in memory with `useState`

Next you'll need to represent the visual states of your component in memory with `useState`. Simplicity is key: each piece of state is a moving piece", and you want as few "moving pieces" as possible. More complexity leads to more bugs!

Start with the state the absolutely must be there. For example, you'll need to store the `answer` for the input, and the `error` (if it exists) to store the last error:

```javascript
cont [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
```

Then, you'll need a state variable representing which one of the visual states described earlier you want to display. There's usually more than a single way to represent that in memory, so you'll need to experiment with it.

If you struggle to think of the best way immediately, start by adding enough state that you're definitely sure that all the possible visual state are covered:

```javascript
const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);
```

Your first idea likely won\'t be the best, but that's okay--refactoring state is part of the process!

### Step 4: Remove any non-essential state variables

You want to avoid duplication in the state content so you're only tracking what is essential.


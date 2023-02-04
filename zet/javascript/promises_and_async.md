# JavaScript Promises and Async Programming.

Asynchronous programming is often intimidating, its' not how we normally
consider writing code.

> ### What we will be learning
>
> * Asynchronous programming
> * Consuming and creating promises
> * Using Async/Await in JavaScript
> * How to work with promise based libraries


The code below makes two separate HTTP requests:

```javascript
export function reaceCondition() {
  let xhr = new XMLHttpRequest();
  let statuses = [];
  xhr.open("GET", "http://localhost:3000/orderStatuses");
  xhr.onload = () => {
    statuses = JSON.parse(xhr.resonseText);
  };

  xhr.send();

  let xhr2 = new XMLHttpRequest();
  xhr2.open("GET", "http://localhost:3000/orders/1");
  xhr2.onload = () => {
    const order = JSON.parse(xhr2.responseText);
    const description = statuses.map(t => {
      if (t.id === order.orderStatusId) {
        return t.description;
      }
    })[0];

    setText(`Order Status: ${description}`);
  };

  xhr2.send();
}
```

These two conditions will most likely be out of order. If they become out of order. There will be no list. Producing an undefined response in JavaScript.

We can fix this. With a little bit of code moved around.

## Building a Callback Pyramid

```javascript
export function callbacks() {
  let xhr = new XMLHttpRequest();
  let statuses = [];
  xhr.open("GET", "http://localhost:3000/orderStatuses");

  xhr.onload = () => {
    statuses = JSON.parse(xhr.responseText);

    letxhr2 = new XMLHttpsRequest();
    xhr2.open("GET", "http://localhost:3000/orders/1");

    xhr2.onload = () => {
      const order = JSON.parse(xhr2.responseText);

      const description = statuses.map(t => {
        if (t.id === order.orderStatusId) {
          return t.description;
        }
      })[0];

      setText(`Order Status: ${description}`);
    };

    xhr2.send();
  }
}
```

This works because we nest one onload function inside of the other. Now the second call is buried in the success of the first call. What problems could come from this?

## Callback Pyramid of Doom

> Why is the pyramid bad?

A common problem that arises when a program uses many levels of nested indentation to control access to a function.

Why "Pyramid"?

```javascript
a("test", (err, aResult) => {
  b(aResult, (err, bResult) => {
    c(bResult, (err, cResult) => {
      d(cRedult);
    });
  });
});
```

It makes a small pyramid if you can see it. What make it bad?

* It makes dirty code and exposes new errors.

```javascript
xhr.onload = () => {
  xhr2.onload = () => {
    const order = JSON.parse(xhr2.responseText);
    xhr3.onload = () => {
      const payments = JSON.parse(xhr2.responseText);
      xhr4.onload = () => {
        const paymentType = JSON.parse(xhr4.responseText);
      };
    };
  };
};
```

There is a lot going on here. It takes some time to process and read it. There is actually a bug in here as well.

How do you handle errors?

```javascript
xhr.onload = () => {
  xhr2.onload = () => {
    xhr3.onload = () => {
      xhr4.onload = () => {};
      xhr4.onerror = () => {};
    };
    xhr3.onerror = () => {};
  };
  xhr2.onerror = () => {};
};
xhr.onerror = () => {};
```

This could work but it just keeps getting messier. How do we get this under control?

## Solving the Callback Pyramid

* `Promise`: Object that represents the `eventual completion` (or failure) of an `asynchronous operation`, and its resulting value.

The value can come instantly or it can come sometime in the future.

It allows us to write asynchronous code in a more clear and less error prone manner.

### Promise States

* `Pending`
* `Fulfilled`
* `Rejected`

Most people talk about a promise being `Resolved` or `Rejected`. This is if the function is successful or it fails the operation.

Promises are not lazy. They are eagerly evaluated.

## Setting up the Sample Project

We will be using `Carved Rock Fitness` an online sporting goods retailer.

Their API consists of:

* `Addresses`
* `Items`
* `Orders`
* `Users`
* `ItemCategories`
* `OrderStatuses`

The projects files can be found ![Github](https://github.com/taylonr/async-programming-promises).

There should be 5 branches in the github.

* Consuming Promises
* Creating Promises
* Iterating
* Understanding Promises
* Master

You can enter code as we go along. This will help build some of the muscle memory. I'll be able to learn better.

### Encountering Promises

```javascript
import { get } from 'axios';
```

Abstraction on top of XMLHttpRequest.

```javascript
export function get() {
  axios.get("http://localhost:3000/orders/1");
}
```

> Side note when you go to the localhost. Go to `/home` to find the
> project folders.

We will use the `/consuming` page. So we can click on the buttons and see the response in the dev tools.

```javascript
export function get() {
  axios.get("http://localhost:3000/orders/1")
  .then(({data}) => {
    setText(JSON.stringify(data));
  });
}
```

Just like that you've consumed a promise.

It starts by calling an asynchronous function and chains on a then event. Inside of the then call. You setText with the JSON stringified data.

### Handling Errors with Promises

You can get lot's of different errors. Let's see how to handle those.

```javascript
export function getCatch() {
  axios.get("http://localhost:3000/orders/1").then(result => {
    if (result.status === 200) {
      setText(JSON.stringify(data));
    } else {
      setText("Error");
    }
  });
}
```

Let's force this to be unsuccessful for a second.

```javascript
export function getCatch() {
  axios.get("http://localhost:3000/orders/89423o8").then(result => {
    if (result.status === 200) {
      setText(JSON.stringify(data));
    } else {
      setText("Error");
    }
  });
}
```

We tried to get a record that didn't exist. The `404` is expected. But the other errors are not. We are in the rejected state and the `.then()` is not handling the error this way. We need to change this around.

```javascript
export function getCatch() {
  axios.get("http://localhost:3000/orders/89423o8").then(result => {
    setText(JSON.stringify(data));
  })
  .catch(err => setText(err));
}
```

Refresh the Failed GET button and it should now show the Error inside of `setText`. What you do in your catch block depends on the application you're trying to create. 

### Chaining Promises

The nice part about `.then` and `.catch` return promises. This means you can chain them together.

```javascript
acios.get("http://locahost:3000/orders/1").then(({ data }) => {
  setText(JSON.stringify(data));
  return "Pluralsight;
})
.then(result => console.log(result));
```

Now let's modify the chain function in the project folder.

```javascript
export function chain() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  });
}
```

We could add a `.then` at the end of the second get. But now we are in another callback hell. Let's chain it off of the other then.

```javascript
export function chain() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.city}`);
  });
}
```

This seem interesting but I expect it to fail. I'm not sure this is the best way to make it happen. There is an error in the console.

The first then we didn't return anything so it returned undefined. It then wraps that up and returns that to the second `get` call.

We have to make sure that we return the `axios.get()` call.

```javascript
export function chain() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    return axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.city}`);
  });
}
```

This will make it work now. Let's read the code from top to bottom. It gets the order, get's the orders address, and display the address.

Our code is getting a bit more manageable from the callback hell from before.

Just remember the return the promise inside if you plan to chain them.

## Catching Errors in a Chain

We are going to work on some error handling:

```javascript
export function chainCatch() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    return axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.city}`);
  });
}
```

In order to get the same error. Let's take away the return from before.

```javascript
export function chainCatch() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.city}`);
  })
  .catch(err => setText(err));
}
```

This should update the result block with the error.

We have caught and handled the error in the promise chain. To remove the error that was popping up in the console previously.

```javascript
export function chainCatch() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    return axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.my.city}`);
  })
  .catch(err => setText(err));
}
```

This is another error and it's because of the `data.my.city` call. There is no `my` in the data.

```javascript
export function chainCatch() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);

    throw new Error("Error!");
  })
  .catch(err => {
    setText(err);
    return { data: {}};
  .then(({ data }) => {
    setText(`City: ${data.my.city}`);
  })
  .catch(err => setText(err));
}
```

Now we throw an error in the first `get`. We will add another catch. In this now first `catch` in the chain. We set the text and we still return data. We return it so that the rest of the chain does not break.

The first catch will only catch errors from the first then.

The second catch will catch all the errors from the first and second.

```javascript
export function chainCatch() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);

    throw new Error("Error!");
  })
  .catch(err => {
    setText(err);
    throw new Error("Get to second catch");
  .then(({ data }) => {
    setText(`City: ${data.my.city}`);
  })
  .catch(err => setText(err));
}
```

In the example above. The first catch will throw the second catch an error. Like a waterfall effect.

```javascript
export function chainCatch() {
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    return axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.my.city}`);
  })
  .catch(err => setText(err));
}
```

Most of the time you might just want 1 catch statement. If you have lots of catches. It could get complicated. Remember to make sure to handle the error throughly. Next we can use promises.

## Performing One Last Operation

Common Use of Promises

Using a library like Axios to make HTTP request. Sometimes pages want to show an indicator when the call is being called. Then remove the indicator when the promise settled. Then we want to show the data.

Showing an Indicator

* Fulfilled
* Rejected

```javascript
export function final() {
  showWaiting();
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    return axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.my.city}`);
  })
  .catch(err => setText(err));
}
```

Before you make the Axios request run `showWaiting()`. Show waiting is imported at the top of the file. The green box covers the screen but never gets hidden.

```javascript
export function final() {
  showWaiting();
  axios.get("http://localhost/3000/orders/1").then(({ data }) => {
    return axios.get('http://localhost:3000/addresses/${data.shippingAddress}`);
  })
  .then(({ data }) => {
    setText(`City: ${data.my.city}`);
  })
  .catch(err => setText(err))
  .finally(() => {
    setTimeout(() => {
      hideWaiting();
    }, 1500);
    appendText(" -- Completely Done");
  });
}
```

Conveniently there is a `finally` at the end we can use. This will run after every thing else finishes. We set the timeout so there is a small delay. It should work and switch back after loading.

This allows you to run async code. Make something happen and then finish at then end.

* Pending
* Fulfilled
* Rejected

### How do I create my own promises?

## Creating and Queueing Promises

## Promise States

* Fulfilled
* Rejected
* Pending

Pending is a promise that has not yet settled.

* `Promise`: Object that represents the eventual completion (or failure) of an asynchronous operation, and its resulting value.

A promise is an object. `let temp = new Promise();`

Now we are in the `creating.mjs` file:

```javascript
import setText, { appendText } from './results.mjs';

export function timeout() {}

export function interval() {}

export function clearIntervalChain() {}

export function xhr() {}

export function allPromises() {}

export function allSettled() {}

export function race() {}
```

Let's start with our first function:

```javascript
export function timeout() {
  const wait = new Promise();

  wait.then(text => setText(text));
}
```

Let's call our variable `wait`. Eventually we want to have the state flip to the fulfilled state so let's add ` wait.then(text => setText(text))`

How do we make the promise change state?

* Take a function as the `executor function` to the constructor.
* put `setTimeout` inside the function.
* Called 1500ms after calling the promise.
* We need to add the `resolve` inside the promise.

```javascript
new Promise((resolve) => {
  setTimout(() => {
    resolve("timeout!");
  }, 1500);
});
```

Our then function got called one time and only one time.

Now let's do something that updates multiple times.

## Understanding Promise states

```javascript
export function interval() {
  const wait = new Promise((resolve) => {
    setInterval(() => {
      resolve("timeout!");
    }, 1500);
  });


  wait.then(text => setText(text));
}
```

* `setInterval` fires multiple times. This is once every 1.5 seconds.

Let's make a counter and update it in the function.

```javascript
export function interval() {
  let counter = 0;
  const wait = new Promise((resolve) => {
    setInterval(() => {
      console.log("INTERVEL");
      resolve(`timeout! ${++counter}`);
    }, 1500);
  });


  wait.then(text => setText(text));
}
```

Let's see what happens in the browser. The response box should update the timeout! 1. The second interval is logged to the console. So no matter how long we wait here the view doesn't update. It only does in the console. So we know the code is running. Now we need to make it work how we need to.

How do we do that?

```javascript
export function interval() {
  let counter = 0;
  const wait = new Promise((resolve) => {
    setInterval(() => {
      console.log("INTERVEL");
      resolve(`timeout! ${++counter}`);
    }, 1500);
  });


  wait.then(text => setText(text))
    .finally(() => appendText(` -- Done ${counter}`));
}
```

Once again it show's timeout! 1 -- Done 1. The interval keeps going but nothing really happens.

Remember when we talked about states. `Settled` or `resolved`.

If the associated promise has already been resolved, either to a value, a rejection, or another promise, this method [resolve] does nothing. If you try to resolve again nothing will happen.

How can we get it to keep going. If we try to settle it again it won't have any effect. How do we get the effect?

```javascript
export function clearIntervalChain() {
  let counter = 0;
  let interval;
  const wait = new Promise((resolve) => {
    interval = setInterval(() => {
      console.log("INTERVEL");
      resolve(`timeout! ${++counter}`);
    }, 1500);
  });

  wait.then(text => setText(text))
    .finally(() => clearInterval(interval));
}
```

Just a few tweets. Putting the interval into a value. This way we can call `clearInterval(interval)` in the finally block. To clean up after the promise and cancel the interval.

## Rejecting a Promise

Let's head to the `creating.mjs` file and go to the `hxr()` function.

There should be an error because there is no user in the fitness API. 

This runs similar to the `xhr` from before except that it's wrapped in a promise. The promise will return a `resolve` or `reject`. That's really the only difference from before. It's wrapped in the promise with the ability to pipe.

```javascript
export function xhr() {
  let request = new Promise((resolve, reject) => {
    let xhr = new XMLHttpRequest();
    xhr.open("GET", "http://localhost:3000/user/7");
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject("Request failed");
    xhr.send();
  });

  request.then(result => setText(result))
    .catch(reason => setText(reason));
}
```

The executor function takes a second parameter `reject`. We will then add a catch at the bottom. The response shows an empty object and not an error. Why is that? Our promise wasn't rejected.

The `xhr` only calls `onerror` when there is a network error or failed to be completed. All others are in the `onload`:

```javascript
xhr.onload = () => {
  if (xhr.status === 200) {
    resolve(xhr.responeText);
  } else {
    reject(xhr.statusText);
  }
}
```

This was it will reject if it's anything but the `200` response. Now the error `404 not found` comes back from the server. Catching the error correctly. The full code is:

```javascript
export function xhr() {
  let request = new Promise((resolve, reject) => {
    let xhr = new XMLHttpRequest();
    xhr.open("GET", "http://localhost:3000/user/7");
    xhr.onload = () => {
      if (xhr.status === 200) {
        resolve(xhr.responeText);
      } else {
        reject(xhr.statusText);
      }
    }
    xhr.onerror = () => reject("Request failed");
    xhr.send();
  });

  request.then(result => setText(result))
    .catch(reason => setText(reason));
}
```

You have the power to control how a promise is settled. `fulfilled` or `rejected`

## Waiting for All Promises to Resolve

### Metadata Types

* Job Type
* Job Status
* Account Type

We might have needed 10 pieces of metadata to make something work. That didn't need to be used all at once. But I didn't need to load the user metadata, and then the account metadata. I just needed to get the data I needed as I needed it. I didn't want sequential calls.

With promises we can make this better. Batching up all the calls and firing them all at once. This is the power of async programming. We can still tell the program to stop until all the promises settle.

```javascript
export function allPromises() {
  let categories = axios.get("http://localhost:3000/itemCategories");
  let statuses = axios.get("http://localhost:3000/orderStatuses");
  let userTypes = axios.get("http://localhost:3000/userTypes");

  Promise.all([categories, statuses, userTypes])
    .then(([cat, stat, type]) => {
      setText("");

      appendText(JSON.stringify(cat.data));
      appendText(JSON.stringify(stat.data));
      appendText(JSON.stringify(type.data));

    });
```

First we bring in a few promises. How do we wait until all 3 are filled. 

Let's use `Promise.all()`. It give the promises back in an array in the order they are called. This allows us to destructure the array and use it.

This dumps out a whole bunch of data to the screen. You can see the calls are all made to the api in the console.

What happens if one of the calls fails? This example will fail on the new axios get. Now we need to add a catch statement after the then.

```javascript
export function allPromises() {
  let categories = axios.get("http://localhost:3000/itemCategories");
  let statuses = axios.get("http://localhost:3000/orderStatuses");
  let userTypes = axios.get("http://localhost:3000/userTypes");
  let addressTypes = axios.get("http://localhost:3000/addressTypes");

  Promise.all([categories, statuses, userTypes, addressTypes])
    .then(([cat, stat, type, address]) => {
      setText("");

      appendText(JSON.stringify(cat.data));
      appendText(JSON.stringify(stat.data));
      appendText(JSON.stringify(type.data));
      appendText(JSON.stringify(address.data));

    }).catch(reason => setText(reason));
}
```

The all function will wait until all functions are fulfilled. Or fail on the first rejection.

How do we get independent data from calls. I don't want it to fail on 1 reject.

## Settling All Promises

Now let's get back as much data as possible without failing on a reject.
Let's change the name from `all` to `allSettled`. It's similar to all, but
it's different. The data returned is different.

`resolved.js`:

```javascript
{
 status: "fulfilled",
 value: {}
}
```

`rejected.js`:

```javascript
{
 status: "rejected",
 value: {}
}
```

You don't need a catch block because it will return all the data. Even the rejected ones. Even though you don't need the catch. It's recommended to get any other errors that pop up.

```javascript
export function allSettled() {
  let categories = axios.get("http://localhost:3000/itemCategories");
  let statuses = axios.get("http://localhost:3000/orderStatuses");
  let userTypes = axios.get("http://localhost:3000/userTypes");
  let addressTypes = axios.get("http://localhost:3000/addressTypes");

  Promise.allSettled([categories, statuses, userTypes, addressTypes])
    .then((value) => {
      let results = value.map(v => {
        if(v.status === 'fulfilled') {
          return `FULFILLED: ${JSON.stringify(v.value.data[0])} `;
        }

        return `REJECTED: ${v.reason.message} `;
      });

      setText(results);
    }).catch(reason => {
      setText(reason));
    }):
}
```

This works well if you use the `allSettled` and expect a different type of data back. It brings more flexibility over `Promise.all`

## Racing Promises

Race conditions can cause problems. Imagine you have a few copies of your endpoint deployed. You want the fastest data. Using the closest endpoint. The time to get data will always vary. It would be nice to always get the data from the fastest location. Promises can help simplify this.

```javascript
export function race() {
  let users = axios.get("http://localhost:3000/users");
  let backup = axios.get("http://localhost:3001/users");

  Promise.race([users, backup])
    .then(users => setText(JSON.stringify(users.data)))
    .catch(reason => setText(reason));
```

Race will return with the first one the settles. That also mean the first one can fail. That the data is completely lost.

Race is a rare function.

## Secrets of async/await

It's time to learn and de-mistify the useful syntax.

## Iterating with Async/Await

JavaScript promises are kind of old. They've already been replaced by async/await. What the point of having it. It makes it easier to use promises in syncronous format. Think of it as syntactic sugar. Designed to make things easier to read and express.

### Two Keywords

* `async`
  - Function: `async function getNames() { return []; }`
  - Fat Arrow: `const getNames = async () => { return []; }`
  - This function will return an implicit promise
  - Return value is wrapped in a promise (could be a rejected promise)
* `await`
  - Must be used inside of `async`
  - Only blocks the current function
  - `const getNames = async () => { await someFunc(); doSomethingElse(); }`
  - This would stop the `doSomethingElse` function from running until the `someFunc()` is done
  - It would not stop another function from executing. Only the functions inside of the `async`

## Awaiting a Call

Let's get the `iterating.mjs` file:

```javascript
import setText, {appendText} from './results.mjs';

export function get() {}

export function getCatch() {}

export function chain() {}

export function concurrent() {}

export function parallel() {}
```

We will start with the first one `get()`:

```javascript
export function get() {
  const {data} = await axios.get("http://localhost:3000/orders/1");
  setText(JSON.stringify(data));
}
```

We use the await with `axios`. You can destucture the data because of the await call. There is actually an error on the function above.

```javascript
export async function get() {
  const {data} = await axios.get("http://localhost:3000/orders/1");
  setText(JSON.stringify(data));
}
```

This time it works. This is because we added `async` to the function. 

`promise`:

```javascript
axios.get("/orders/1");
.then(({data}) => {
  setText(JSON.stringify(data))
});
```

`await`:

```javascript
const {data} = await axios.get("/orders/1");

setText(JSON.stringify(data));
```

The above two examples are the same thing. One is a promise and the other is an await.

What happens here when a call like await fails?

## Handling Errors with Async/Await

Change the route to get orders 123. This will fail. We will use the `try` and `catch` block. You now have the ability to handle the error for synchronous and asynchronous code.

```javascript
export async function getCatch() {
  try {
    const {data} = await axios.get("http://localhost:3000/orders/123");
    setText(JSON.stringify(data));
  } catch (error) {
    setText(error);
  }
}
```

## Chaining Async/Await

Going into the `consuming.js` file and find the `chain()` function. To be reminded what we did with promises. Get the order 1 and make another API call the get the shipping information. Then we set the text of the city that it was shipped to.

```javascript
export async function chain() {
  const {data} = await axios.get("http://localhost:3000/orders/1");
  const {data: address} = await axios.get(`http://localhost:3000/addresses/${data.shippingAddress}`);
  setText(`City: ${JSON.stringify(address.city)}`);
}
```

It is the same result we get with promises. There is less code and it's a bit cleaners.

How can I made a non-sequential call?

## Awaiting Concurrent Requests

Sometimes you don't want functions to depend on other functions.

```javascript
export async function concurrent() {
  const orderStatus = axios.get("http://localhost:3000/orderStatuses");
  const orders = axios.get("http://localhost:3000/orders");

  setText("");

  const {data: statuses} = await orderStatus;
  const {data: order} = await orders;

  appendText(JSON.stringify(statuses));
  appendText(JSON.stringify(order[0]));
}
```

If you click on the concurrent button. By not putting the await on at first it allowed the functions to kick off first. That's why the order's functions completed before the status. They started at the same time. The code ran at the same time. Even though we asked the slower request to start first.

What if we wanted to handle the same two calls. But handle the order they come back.


## Awaiting Parallel Calls

```javascript
export default async function parallel() {
  setText("");

  await Promise.all([
    (async () => {
      const {data} = await axios.get("http://localhost:3000/orderStatuses");
      appendText(JSON.stringify(data));
    })(),
    (async () => {
      const {data} = await axios.get("http://localhost:3000/orders");
      appendText(JSON.stringify(data));
    })()
  ]);
}
```

With this construct we have two promises in promise.all. Since we are awaiting the promise.all it won't end until they all end. If you run this the orders finishes before order status.

With this approach. This is handled as the promise settles.

# Summary

There are 3 states

* Pending
* Fulfilled
* Rejected

* `let temp = new Promise((resolve, reject) => {});`
* `Async/await`

Getting code to work together. Making it concurrent and even parallel. 

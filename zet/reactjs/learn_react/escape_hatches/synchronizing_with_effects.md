# Synchronizing with Effects

Some components need to synchronize with external systems. For example, you might want to control a non-React component based on the React state, set up a server connection, or send an analytics log when a component appears on the screen. Effects lets you run some code after rendering so that you can synchronize your component with some system outside of React.

> #### You will learn
>
> * What Effects are
> * How Effects are different from events
> * How to declare an Event in your component
> * How to skip re-running an Effect unnecessarily
> * Why Effects run twice in development and how to fix them

## What are Effect and how are they different form events?

Before getting to Effects, you need to be familiar with two types of logic inside React components:

* `Rendering code` (introduced in Describing the UI) lives at the top level of your component. This is where you take the props and state, transform them, and return the JSX you want to see on the screen. Rendering code must be pure. Like a math formula, it should only calculate the result, but not do anything else.
* `Event handlers` (introduced in Adding Interactivity) are nested functions inside your components that do things rather than just calculate them. An event handler might update an input field, submit an HTTP POST request to buy a product, or navigate the user to another screen. Event handlers contain "side effects" (they change the program's state) and are caused by a specific user action (for example, a button click or typing).

Sometimes this isn't enough. Consider a `ChatRoom` component that must connect to the chat server whenever it's visible on the screen. Connecting to a server is not a pure calculation (it's a side effect) so it can't happen during rendering. However, there is no single particular event like a click that causes `ChatRoom` to be displayed.



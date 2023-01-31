# Manipulating the DOM with Refs

React automatically updates to DOM to match your render output, so your component won't often need to manipulate it. However, sometimes you might need access to the DOM elements managed by React--for example, to focus a node, scroll to it, or measure its size and position. there is no built-in way to do those things in React, so you will need a ref to the DOM node.



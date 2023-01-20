# Choosing the State Structure

Structuring state well can make a difference between a component that is
pleasant to modify and debug, and one that is a constant source of bugs.
Here are some tips you should consider when structuring state.

> ### You will learn
>
> * When to use a single vs multiple state variables
> * What to avoid when organizing state
> * How to fix common issues with the state structure

## Principles for structuring state

When you write a component that holds some state, you'll have to make choices about how many state variables to use and what the shape of their data should be. While it's possible to write correct programs even with a suboptimal state structure, there are a few principles that can guide you to make better choices:

1. Group related state. If you always update two or more state variables at the same time, consider merging them into a single state variable.
2. 

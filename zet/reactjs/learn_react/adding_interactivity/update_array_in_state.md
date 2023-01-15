# Updating Arrays in State

Arrays are mutable in JavaScript, but you should treat them as immutable
when you store them in state. Just like with objects, when you want to
update an array stored in state, you need to create a new one (or make a
copy of an existing one), and then set state to use the new array.

> ### You will learn
> 
> * How to add, remove, or change items in an array in React state
> * How to update an object inside of an array
> * How to make array copying less repetitive with Immer
>

## Updating arrays without mutation

In JavaScript, arrays are just another kind of object. Like with
objects, you should treat arrays in React state as read-only. This means
that you shouldn't reassign items inside an array like `arr[0] =
'bird'`, and you also shouldn't use methods that mutate the array, such
as `push()` and `pop()`.



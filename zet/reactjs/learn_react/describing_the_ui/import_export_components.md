# Importing and Exporting Components

The magic of components lies in their reusability: you can create
components that are composed of other components. But as you nest more
and more components, it often makes sense to statrt splitting them into
different files. This lets you keep your files easy to scan and reuse
components in more places. 

> ### You will learn
>
> * What a root components file is
> * How to import and export a component
> * When to use default and named imports and exports
> * How to import and export multiple components from one file
> * How to split components into multiple files
>

## The root component file

In `Your First Component`, you made a `Profile` component and a
`Gallery` component that renders it:

```javascript



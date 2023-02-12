# Separating Events from Effects

Event handlers only re-run when you perform the same interaction again.
Unlike event handler, Effects re-synchronize if some value they read,
like a prop or a state variable, is different from what it was during
the last render. Somethings, you also want a mix for both behaviors: an
Effect that re-runs in response to some values but not others. This page
will teach you how to do that.

> #### You will learn
>
> * How to choose between an event handler and an Effect
> * Why Effects are reactive, and event handlers are not
> * What to do when you want a part of your Effect's code to not be
    reactive
> * 
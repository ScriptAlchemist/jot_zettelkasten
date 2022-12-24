# Dynamic Routes

The `/about` route created on the last page is pretty static. It does
not matter what query of path parameters are passed to the route, it
will always render the same page. Let's create a `/greet/:name` that
will render a page with a greeting that contains the name passed in the
path.

Before diving in, a quick refresher on "dynamic" routes. Dynamic routes
don't just match a single static path, but rather a whole bunch of
different paths based on a pattern. For example, the `/greet/:name`
route will match the paths `/greet/Luca` and `/greet/John`, but not
`/greet` or `/greet/Luca/John`.

Fresh supports dynamic routes out of the box through file system routing.
To make any path segment dynamic, just put square brackets around that
segment in the file name. For example the `/greet/:name` route maps to
the file name `/routes/greet/[name].tsx`.

Just like the static `/about` route, the dynamic `/greet/:name` route
will render a page. The module must once again expose a component as a default export. This time the component will receive the matched path segment properties as arguments in its `props` object through.

```typescript
 import { PageProps } from "$fresh/server.ts";

 export default function GreetPage(props: PageProps) {
   const { name } = props.params;
   return (
     <main>
       <p>Greetings to you, {name}!</p>
     </main>
   );
 }
```

The `PageProps` interface actually contains a bunch of useful properties that can be used to customize the rendered output. Next to the matched url pattern parameters, the raw `url`, and the `route` name can also be found in here.

Navigating to `https://localhost:8000/gree/Luca` will now render a page showing, "Greetings to you, Luca!".

The `Concepts: Routing` page has more information about dynamic routes, especially about how to create more advanced dynamic routes.

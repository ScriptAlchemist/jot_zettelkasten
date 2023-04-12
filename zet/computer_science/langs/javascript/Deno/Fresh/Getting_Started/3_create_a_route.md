# Create A Route

After getting the project running locally, the next step is to add a new
route to the project. Routes encapsulate the logic for handling request to a particular path in your project. They can be used to handle API requests or render HTML pages. For now we are going to do the latter. 

Routes are defined as files in the `routes` directory. the file name of the module is important: it is used to determine the path that the route will handle. for example, if the file name is `index.js`, the route will handle requests to `/`. If the file name is `about.js`, the route will handle requests to `/about`. If the file name is `contact.js` and is placed inside of the `routes/about/` folder, the route will handle requests to `/about/contact/`. This concept is called `File-system routing`. You can learn more about it on the `Concepts: Routing` page.

Route files that render HTML are JavaScript or TypeScript modules that export a JSX component as their default export. This component will be rendered for every request to the route's path. The component receives a few properties that can be used to customize the rendered output, such as the current route, the `url` of the request , and handler data (more on that later).

In the demo project we'll create a route to handle the `/about` page. To do this, one needs to create a new `routes/about.tsx` file. In this file, we can declare a component that should be rendered every time a user visits the page. This is done with JSX.

```typescript
// routes/about.tsx

export default function AboutPage() {
    return (
        <main>
            <h1>About</h1>
            <p>This is the about page.</p>
        </main>
    );
}
```

The new page will be visible at `http://localhost:8000/about`.

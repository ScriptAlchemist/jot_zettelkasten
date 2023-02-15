# Removing Effect Dependencies

When you write an Effect, the linter will verify that you've included
every reactive value (like props and state) that the Effect reads in the
list of your Effect's dependencies. This ensures that your Effect
remains synchronized with the latest props and state of your component.
Unnecessary dependencies may cause your Effect to run too often, or even
create an infinite loop. Follow this guide to review and remove
unnecessary dependencies from your Effects.

> #### You will learn
>
> * How to fix infinite Effect dependency loops
> * What to do when you want to remove a dependency
> * How to read a value from your Effect without "reacting" to it
> * How and why to avoid object and function dependencies
> * why suppressing the dependency linter is dangerous, and what to do instead

## Dependencies should match the code

When you write an Effect, you first specify how to start and stop
whatever you want your Effect to be doing:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  	// ...
}
```

[Killing It](https://lite.duckduckgo.com/lite?kd=-1&kp=-1&q=Killing%20It)

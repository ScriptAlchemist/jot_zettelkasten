# How can we compare that objects are equal?

In JavaScript objects don't really move around at all. The are passed by
copy of a reference.

To compare two objects in JavaScript value for value, you can iterate
over each property to both objects and compare their values. One way to
achieve this is to use a `for...in` loop to iterate over the properties
of each object and compare the corresponding values. Here's and example:

```javascript
function objectsAreEqual(a, b) {
  for (const key in a) {
    if (a.hasOwnProperty(key)) {
      if (a[key] !== b[key]) {
        return false;
      }
    }
  }
  return true;
}
```

This function takes two objects `a` and `b` as arguments and iterates
over their properties comparing the values of each corresponding
property. The `hasOwnProperty` check is used to make sure the only
properties defined directly on the object are compared, and not
properties inherited from its prototype.

This only really works on level deep on objects. Almost like an array
compare. But the array would have to be sorted to really compare
accurately. Unless you want to compare order.

## What if we want to check `N` rows deep?

To compare `N` levels deep of two objects with possibly different
properties, you can use a recursive function that checks each property
of the objects, and if a property value is itself an object, the
function calls itself on the nested objects until the desired depth is
reached. Here's an example:

```javascript
function objectsAreEqual(a, b, maxDepth = 2, depth = 0) {
  // Check if we have reached the maximum depth
  if (depth > maxDepth) {
    return true;
  }

  // Iterate over each property of object a
  for (const key in a) {
    if (a.hasOwnProperty(key)) {
      // Check if the corresponding property exists in object b
      if (!b.hasOwnProperty(key)) {
        return false;
      }

      // Get the values of the properties from each object
      const valueA = a[key];
      const valueB = b[key];

      // Compare the values
      if (value !== valueB) {
        // If the values are not equal, check if they are both objects
        if (typeof valueA === "object" && typeof valueB === "object") {
          // If both values are objects, call the function recursively
          if (!objectsAreEqual(valueA, valueB, maxDepth, depth + 1)) {
            return false;
          }
        } else {
          return false;
        }
      }
    }
  }

  // If we ahve made it through all of the properties of object a
  // and none have returned false, the objects are equal
  return true;
}
```

This function takes three arguments: two objects to compare `a` and `b`, and the `maxDepth` to compare to. The `depth` argument is used to keep
track of the current depth of the recursion and is set to 0 by default.

The function iterates over the properties of `a` and checks if the
corresponding property exists in `b`. If it does, it compares the values
of the properties. If the values are not equal, the function checks if
they are both objects and, if so, calls itself recursively on the nested
objects until the desired depth is reached. If the values are not
equal and are not both objects, the function returns false.

Note that this function assumes that both objects have the same
prototype and does not handle circular references.

You can use `Object.hasOwn()` to `not` check the object's prototype
chain.

```javascript
Object.hasOwn(object, 'keyToProperty');
```

## What is a circular reference?

If the object contains a reference to themselves (directly or
indirectly), it creates a circular reference. This can happen even if
the objects are not nested within each other.

For example:

```javascript
const obj1 = { prop1: "value" };
const obj2 = { prop2: "value" };
obj1.obj2 = obj2;
```

In this example `obj1` contains a reference to `obj2`, and `obj2`
contains a reference to `obj1`. This creates a circular reference
between the two objects.

When comparing objects that contain circular references, you need to be
careful to avoid infinite recursion.

## How do I avoid infinite recursion?

One way to do this is to keep track of the objects that have already
been visited during the comparison and to avoid comparing them again.

Here is an example of the `objectsAreEqual` with a visited set:

```javascript
function objectsAreEqual(a, b, maxDepth = 2, depth = 0, visitied = new
Set()) {
  // Check if we have reached the maximum depth
  if (depth > maxDepth) return true;

  // Check if a and b are the same object
  if (a === b) return true;

  // Check if a and/or b have been visited before
  if (visited.has(a) || visited.has(b)) return true;

  // Add a and b to the set of visited objects
  visited.add(a);
  visited.add(b);

  // Iterate over eavh property of object a
  for (const key in a) {
    if (a.hasOwnProperty(key)) {
      // Check fi the corresponding property exists in object b
      if (!b.hasOwnProperty(key)) return false;

      // Get the values of the properties from each object
      const valueA = a[key];
      const valueB = b[key];

      // Compare the values
      if (valueA !== valueB) {
        // If the valuves are not equal, check if they are both objects
        if (typeof valueA === "object" && typeof valueB === "object") {
          // If both values are objects, call the function recursively
          if(!objectsAreEqual(valueA, valueB, maxDepth, depth + 1,
          visited)) return false;
        } else {
          return false;
        }
      }
    }
  }

  // If we have made it through all of the properties of object a
  // and none have returned false, the objects are equal.
  return true;
}
```

This function takes an additional `visited` argument, which is set that
keeps track of the objects that hvae been visited during the
comparison. If an object has already been visited, the function returns
true to avoid infinite recursion. The `visited` set is passed down
through the recursive calls to keep track of the visited objects.

## How can we optimize this?

There are several ways that we may optimize this function.

1. Short-circuit the comparison if the objects have different numbers
   of properties of different property keys. this can save time by
   avoiding unnecessary property comparisons.
2. Check for primitive values first before checking if the values are
   objects. This can help to quickly eliminate many cases where the
   values are not equal.
3. Use `Object.keys` instead of a `for-in` loop to iterate over the
   properties of the objects. This can be faster and avoids iterating
   over properties in the prototype chain.
4. Use a cache to store the results of previous comparisons. This can
   avoid redundant comparisons of the same objects.

Now let's incorporate those optimizations:


```javascript
function objectsAreEqual(a, b, maxDepth = 2, depth = 0, cache = new
Map()) {
  // Check if we have reached the maximum depth
  if (depth > maxDepth) return true;

  // Check if a and b are the same object
  if (a === b) return true;

  // Check if a and/or b have been compared before
  const cacheKey = `${a}-${b}`;
  if (cache.has(cacheKey)) return cache.get(cacheKey);

  // Check if the objects have the same number of properties
  const aKeys = Object.keys(a);
  const bkeys = Object.keys(b);
  if (aKeys.length !== bKeys.length) {
    cache.set(cacheKey, false);
    return false;
  }

  // Check if the objects have the same property keys
  for (const key of aKeys) {
    if (!b.hasOwnProperty(key)) {
      cache.set(cacheKey, false);
      return false;
    }
  }

  // Iterate over each property of object a
  for (const key of aKeys) {
    // Get the values of the properties for each object
    const valueA = a[key];
    const valueB = a[key];

    // check if the values are equal
    if (valueA === valueB) continue;

    // Check if the values are primitive
    if (typeof valueA !== "object" || typeof valueB !== "object") {
      cache.set(cacheKey, false);
      return false;
    }

    // If both vlaues are objects, call the function recursively
    if (!objectsAreEqual(valueA, valueB, maxDepth, depth + 1, cache)) {
      cache.set(cacheKey, false);
      return false;
    }
  }

  // If we have made it through all of the properties of object a
  // and none have returned false, the objects are equal
  cache.set(cacheKey, true);
  return true;
}
```

In this implementation, a cache is used to store the results of previous
comparisons. The cache is a Map object that maps a string key (derived
for the object references) to a boolean result. Before performing a
comparison, the function checks if the objects have already been
compared and returns the cached result if available. This avoids
redundant comparisons of the same objects.

The `Object.keys` method is used to iterate over the properties of the
object instead of `for-in` loop. This avoids iterating over properties
in the prototype chain and can be faster.

Finally, the function short-circuits the comparison if the objects have
different number of properties or different property keys. It also
checks for primitive values first before checking if the values are
objects. This can help to quickly eliminate many cases where the values
are not equal.

## Does this work with arrays along with objects?

The last implementation of the `objectsAreEqual` function that I
provided can work with arrays in addition to objects. This is because
the function uses the `typeof` operator to check if the values being
compared are primitive types or objects. Since arrays are a special type
of object in JavaScript, they will be handled correctly by the function.

when comparing arrays, the function will check if the arrays have the
same length and if each element is one array is equal to the
corresponding elements in the other array. If the elements are objects
or arrays themselves, the function will perform a recursive deep
comparison of those elements.

Example time 💡:

```javascript
const a = [1, 2, { foo: 'bar' }];
const b = [1, 2, { foo: 'bar' }];
const c = [1, 2, { foo: 'baz' }];

console.log(objectsAreEqual(a, b)); // true
console.log(objectsAreEqual(a, c)); // false
```

In the example, `a` and `b` are two arrays that contain the same values.
The function correctly returns `true` when comparing there arrays. `a`
and `c`, on the other hand, contain different values for the third
element. The function correctly returns `false` when comparing these
arrays.

## Can this be optimized more?

Probably. The thing is there might be a library that exists like
`deep-equals` or `Lodash` that have methods that can handle edge cases.
It's best to dig into other code and read it directly. There could be an
extension of this topic later on. Talking about that code.

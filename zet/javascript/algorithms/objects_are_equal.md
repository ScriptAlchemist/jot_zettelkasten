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


# 146 LRU Cache

Design a data structure that follows the constraints of a `Least Recently
Used (LRU) cache`

Implement the `LRUCache` class:

* `LRUCache(int capacity)` Initialize the LRU cache with positive size `capacity`.
* `int get(int key)` Return the value of the `key` if the key exists, otherwise return `-1`.
* `void put(int key, int value)` Update the value of the `key` if the `key` exists. Otherwise, add the `key-value` pair to the cache. If the number of keys exceeds the `capacity` from this operation, evict the least recently used key.

The functions `get` and `put` must each run in `O(1)` average time complexity.

## Constraints:

* `1 <= capactiy <= 3000`
* `0 <= key <= 10^4`
* `0 <= value <= 10^5`
* At most `2 * 10^5` calls will be made to `get` and `put`.

## Example

### Input:

| Action     | Returned    |    Cache     |
|   :---:    |   :---:     |     :--:     |
| LRUCache   |   null      | {}.length(n) |
| put(1, 1)  |   null      |    {1=1}     |
| put(2, 2)  |   null      | {1=1, 2=2}   |
| get(1)     |    1        | {1=1, 2=2}   |
| put(3, 3)  |   null      | {1=1, 3=3}   |
| get(2)     |    -1       | {1=1, 3=3}   |
| put(4, 4)  |   null      | {3=3, 4=4}   |
| get(1)     |    -1       | {3=3, 4=4}   |
| get(3)     |    3        | {3=3, 4=4}   |
| get(4)     |    4        | {3=3, 4=4}   |

1. `const LRUCache = new LRUCache(2);` // create a cache with 2 length
2. `LRUCache.put(1, 1);` // cache is {1=1}
3. `LRUCache.put(2, 2);` // cache is {1=1, 2=2}
4. `LRUCache.get(1);` // return 1
5. `LRUCache.put(3, 3);` // cache is {1=1, 3=3}
6. `LRUCache.get(2);` // return -1
7. `LRUCache.put(4, 4);` // cache is {4=4, 3=3}
8. `LRUCache.get(1);` // return -1
9. `LRUCache.get(3);` // return 3
10. `LRUCache.get(4);` // return 4

First we will make the LRUCache function like so:

```javascript
/**
 * @param {number} capacity
 */
const LRUCache = function(capacity) {
  this.capacity = capacity;
  this.cache = new Map();
};
```

In this section we create the capacity and the cache. Next we will create the put method.

```javascript
/**
 * @param {number} key
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function(key, value) {
  if (this.cache.has(key)) this.cache.delete(key);
  this.cache.set(key, value);
  if (this.cache.size > this.capacity) {
    this.cache.delete(this.cache.keys().next().value);
  }
};
```

In the above function we do some deleting if the key already exists in the map. If we delete the key/value we immediately put it back onto the hashmap. This keeps the order and maintains O(1). After we add the key value. We check if the size has raised above the capacity. If the size is past the capacity. We will delete the first key in the hashmap. This is the Least Recently Used. This is because it's on the end of the queue. When we delete and re-add keys that already exist. It's to maintain the order.

Now let's build the `get()` method:

```javascript
/**
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function(key) {
  if (!this.cache.has(key)) return -1;
  const value = this.cache.get(key);
  this.cache.delete(key);
  this.cache.set(key, value);
  return value;
};
```

In the get function you first search if the key exists. If it doesn't you return `-1`. If it does. We save the value in a variable. Then we delete the key from the hashmap. After it is deleted. We re-add the key/value pair. We delete and re-add to keep track of the least recently used.

The full solution:

```javascript
/**
 * @param {number} capacity
 */
const LRUCache = function(capacity) {
    this.capacity = capacity;
    this.cache = new Map();
};

/**
 * @param {number} key
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function(key, value) {
    if (this.cache.has(key)) this.cache.delete(key);
    this.cache.set(key, value);
    if (this.cache.size > this.capacity) {
      this.cache.delete(this.cache.keys().next().value);
    }
};

/**
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function(key) {
    if (!this.cache.has(key)) return -1;
    const value = this.cache.get(key);
    this.cache.delete(key);
    this.cache.set(key, value);
    return value;
};
```

This answer works but it isn't the only answer. There is a solution where we use a double linked list to solve this. We can try to explain it below.







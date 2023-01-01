# 206. Reverse Linked List

Given the `head` of a slightly linked list, reverse the list, and return the reversed list.

Example 1:

```
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

Example 2:

```
Input: head = [1,2]
Output: [2,1]
```

Example 3:

```
Input: head = []
Output: []
```

Constraints:

* The number of nodes in the list is the range `[0, 5000]`.
* `-5000 <= Node.val <= 5000`

Follow up: A linked list can be reversed either iteratively or recursively. Could you implement both?

```
[1] -> [2] -> [3] -> [4] -> [5]
```

Now we need to get it to output:

```
[5] -> [4] -> [3] -> [2] -> [1]
```

To reverse a singly linked list, you can follow these steps:


* Set up three

### Iterative solution

### Recursive solution

```javascript
function reverseLinkedList(head) {
  // Base case: if the head is null or there is only one node, the list
  is already reversed
  if (!head || !head.next) {
    return head;
  }

  // Reverse the rest of the list
  const reversedTail = reverseLinkedList(head.next);

  // Append the head to the end of the reversed list
  head.next.next = head;
  head.next = null;

  // Return the reversed list
  return reversedTail;
}
```

This function takes in the head of a linked list and returns the head of
the reversed linked list. It works by recursively reversing the rest of
the list and then appending the head to the end of the reversed list.

Here is an example of how you could use this function:

```javascript
// Define a linked list with four nodes
const head = {
  value: 1,
  next: {
    value: 2,
    next: {
      value: 3,
      next: {
        value: 4,
        next: null
      }
    }
  }
};

// Reverse the linked list
const reversedHead = reverseLinkedList(head);

// Print the values in the reversed linked list
let current = reversedHead;
while (current) {
  console.log(current.value)
  current = current.next;
}
```

1. The function starts by checking the base case: if the head is null of there is only one node in the list. If either of these conditions are true, the list is already reversed, so the function returns the head of the list.

* `if (!head || !head.next) { return head; }`

2. If the base case is not met, the function calls itself recursively on the next node in the list. This will reverse the rest of the list, starting from the second node.

* `const reversedTail = reverseLinkedList(head.next);`

3. The function then takes the head of the original list and appends it to the end of the reversed list. It does this by setting the `next` property of the second-to-list node in the reversed list to the head of the original list, and then setting the `next` property of the head of the original list to null.

* `head.next.next = head;`
* `head.next = null;`

4. Finally, the function returns the reversed list by returning the reversed tail (the head of the reversed list).

* `return reversedTail;`

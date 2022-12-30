# 21. Merge Two Sorted Lists

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists in a one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.

Example 1:

![Merge Sorted Linked Lists](../../../images/leetcode/21_merge_ex1.jpeg)

```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

Example 2:

```
Input: list1 = [], list2 = []
Output: []
```

Example 3:

```
Input: list1 = [], list2 = [0]
Output: [0]
```

Constraints:

* The number of nodes in both lists is in the range [0,50].
* `-100 <= Node.val <= 100`
* Both `list1` and `list2` are sorted in `non-decreasing` order.


Lets look at the sample code:

```
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} list1
 * @param {ListNode} list2
 * @return {ListNode}
 */
const mergeTwoLists = (list1, list2) => {

};
```

Even though in the examples we make it seem like they are arrays. They are not. It's a Linked List.

```
const testListOne = new ListNode(1,new ListNode(2,new ListNode(4)));
const testListTwo = new ListNode(1,new ListNode(3,new ListNode(4)));
```

Something to notice about the Linked List is:

```
/**
 * @param {number} val
 * @param {number} next
 * @return {ListNode}
 */
function ListNode(val, next) {
  this.val = val || 0;
  this.next = next || null;
}
```

* There is a value
* There is a next or null

Linked lists aren't always the best data structure because you can't use something like an index. You have to traverse the list one node at a time. This can be expensive if you need to loop over the whole list.


Having a linked list we want to be traversing the list until we hit null. Once either list is at `null`. That list is finished being added to the new list.

## Describe the functionality

This is a function that takes in two linked lists `list1` and `list2` and merges them into a single linked list. 

A linked list is a data structure that consists of a sequence of nodes, where each node stores a value and a reference (i.e., a pointer) to the next node in the sequence. In this case, each node is represented by an instance of the `ListNode` class, which has a single property, `val`, that stores the value of the node, and another property, `next`, that stores a reference to the next node in the list.

The functions begins by creating a new dummy node, which will serve as the head of the merged list. The dummy node is not part of the final merged list and is only used as a placeholder to make the implementation simpler. The functions also declares a variable `curr` that will be used to iterate over the merged list and add the nodes to it.

Next, the functions enters a `while` loops that will continue until both `list1` and `list2` are empty. Inside the loop, the function compares the values of the current nodes in `list1` and `list2` and adds the node with the smaller value to the merged list. The node that was added is removed from its original list, and the iterator for the merged list is advanced to the next node.

Finally, when one of the lists is empty, the loop will exit and the function will add the remaining nodes from the other list (if any) to the merged list. The functions then returns the next node of the dummy node, which is the actual head of the merged list.


* `let tempList = new ListNode();`
* `let currentNode = tempList`
* `while (list1 && list2)`
* `if (list1.val < list2.val)`


I need to look and talk about this one:

```
var mergeTwoLists = function(list1, list2) {

    if(list1 === null) return list2;
    if(list2 === null) return list1;

    if(list1.val < list2.val) {
        list1.next = mergeTwoLists(list1.next, list2);
        return list1;
    } else {
        list2.next = mergeTwoLists(list1, list2.next);
        return list2;
    }

};
```

This creates a recursive answer to a problem that was iterative before. I find this one at the bottom beautiful. 









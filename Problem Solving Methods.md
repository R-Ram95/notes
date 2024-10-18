---
tags:
  - Data-Structures-and-Algorithms
---
  

**Sliding Window Pattern**

- used to perform required operation on a specific window size of a given array or linkedlist
- start from the 1st element and keep shifting right by 1

![[/Untitled 27.png|Untitled 27.png]]

can be used when:

- input is a linear datastructure like a linked list, array, or string
- you are asked to find the longest/shorted substring, subarray, desired value

```JavaScript
function slidingWindow(arr) {
    let left = 0, ans = 0, curr = 0;

    for (let right = 0; right < arr.length; right++) {
        // do logic here to add arr[right] to curr

        while (WINDOW_CONDITION_BROKEN) {
            // remove arr[left] from curr
            left++;
        }

        // update ans
    }

    return ans;
}
```

**2 Points or Iterators**

- two pointers iterator through a data structure in tandem until one or both pointers his an end condition

![[/Untitled 1 16.png|Untitled 1 16.png]]

can be used when:

- when dealing with sorted arrays or linked list and you need to find a set of elements that fulfill certain conditions
- set of elements in the array is a pair, triplet, or sub array

  

2 pointers, opposite ends

```JavaScript
fubctuib twoPtrOpposite (arr) {
    let left = 0, ans = 0, right = arr.length - 1;

    while (left < right) {
        // do some logic here with left and right
        if (CONDITION) {
            left++;
        } else {
            right--;
        }
    }

    return ans;
}
```

2 ptrs, 2 inputs, exhaust both

```JavaScript
function twoPtr2input(arr1, arr2) {
    let i = 0, j = 0, ans = 0;
    
    while (i < arr1.length && j < arr2.length) {
        // do some logic here
        if (CONDITION) {
            i++;
        } else {
            j++;~
        }
    }

    while (i < arr1.length) {
        // do logic
        i++;
    }

    while (j < arr2.length) {
        // do logic
        j++;
    }

    return ans;
}
```

**Fast and Slow Pointers or Iterators**

- 2 pointer technique which move through the array at different speeds
- useful when dealing cyclic linked lists or arrays
- the fast ptr should catch the small ptr once both pointers are in a cyclic loop

![[/Untitled 2 15.png|Untitled 2 15.png]]

Can be used when:

- problem deals with a loop in a linked list or array
- when you need to know the position of a certain element or overall length of a linked list

```JavaScript
function fastSlow(head) {
    let slow = head;
    let fast = head;
    let ans = 0;
    
    while (fast && fast.next) {
        // do logic
        slow = slow.next;
        fast = fast.next.next;
    }
    
    return ans;
}
```

**Reverse a Linked List:**

- initialize:
    - previous node = null
    - current node = head
    - next node in while loop
- next = current.next
- [current.next](http://current.next) = previous
- previous = current
- current = next

![[/Untitled 3 15.png|Untitled 3 15.png]]

```JavaScript
function reverseLinkedList(head) {
    let curr = head;
    let prev = null;
    while (curr) {
        let nextNode = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nextNode;
    }

    return prev;
}
```

  

**Tree BFS**

- search each level before moving on to the next
- uses queue to track all nodes of a level before moving to the next
- used for level-by-level search
- add root to queue
- while the queue is non-empty
    - read the node at the head of the queue
    - add the nodes children to the queue

![[/Untitled 4 10.png|Untitled 4 10.png]]

```JavaScript
function treeBFS(head) {
    let queue = [root];
    let ans = 0;

    while (queue.length) {
        let currentLength = queue.length;
        // do logic for current level

        let nextQueue = [];

        for (let i = 0; i < currentLength; i++) {
            let node = queue[i];
            // do logic
            if (node.left) {
                nextQueue.push(node.left);
            }
            if (node.right) {
                nextQueue.push(node.right);
            }
        }

        queue = nextQueue;
    }

    return ans;
}
```

  

**Tree DFS**

- use recursion to keep track of previous node (parent) while traversing
- start at parent, if node is not a leaf do three things:
    
    1) pre order ⇒ process the current node now
    
    2) in order ⇒ process current node between the two children
    
    3) post order ⇒ process the current node after the two children
    
- make recursive calls for both the children of the current node

```JavaScript
function treeDFS(root)=> {
    if (!root) {
        return;
    }

    let ans = 0;

    // do logic
    treeDFS(root.left);
    treeDFS(root.right);
    return ans;
}
```

  

**Binary Search**

- when you are asked to find an element in a sorted array or matrix
- you cut your search space down by a half after every iteration

![[/Untitled 5 9.png|Untitled 5 9.png]]

Steps:

1) find middle, start, and end

middle = start + (end - start) / 2 ⇒ we add start to prevent integer overflow

2) if key === index middle ⇒ return middle

3) if key < arr[middle] ⇒ element is in first half of space ⇒ end = middle - 1

4) if key > arr[middle] ⇒ element is in second half of space ⇒ start = middle + 1

  

```JavaScript
function binarySearch (arr, target){
    let left = 0;
    let right = arr.length - 1;
    while (left <= right) {
        let mid = Math.floor((left + right) / 2);
        if (arr[mid] == target) {
            // do something
            return;
        }
        if (arr[mid] > target) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }

    // left is the insertion point
    return left;
}
```
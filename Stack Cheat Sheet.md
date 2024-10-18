---
tags:
  - Data-Structures-and-Algorithms
---
> [!important]  
> SummaryLast in First Out (LIFO)Linked List representationArray Representation  

  

> [!important]  
> API:StackOfStrings() - create an empty stackvoid push(String item) — insert a new string onto stackString pop() - remove and return the string most recently addedboolean isEmpty() - is the stack empty? int size() - number of strings on the stack  

  

> [!important]  
> LinkedList RepresentationEach item is stored in a node of a linkedlistto pop()make first node point to next nodeto push(item) create reference to first nodereassign first node to new nodeinsert item into new nodeassign the new nodes next to reference of old first nodeEvery operation takes constant time in the worst case  

  

> [!important]  
> Array Representationeach item stored as element in an arrayneed to grow and shrink array on push() and pop() callspush() - double array size when we run out of spacepop() - halve size of array when array is 1/4 full![[image.png]]  

  

> [!important]  
> CompareLinked-list implementation.Every operation takes constant time in the worst case.Uses extra time and space to deal with the links.Resizing-array implementation.Every operation takes constant amortized time.Less wasted space.
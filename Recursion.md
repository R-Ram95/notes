---
tags:
  - Fundamentals
---
Reference

> [!info] How Recursion Works - Explained with Flowcharts and a Video  
> Illustration (and all in this article) by Adit Bhargava> "In order to understand recursion, one must first understand recursion.  
> [https://www.freecodecamp.org/news/how-recursion-works-explained-with-flowcharts-and-a-video-de61f40cb7f9/](https://www.freecodecamp.org/news/how-recursion-works-explained-with-flowcharts-and-a-video-de61f40cb7f9/)  

  

- there are no performance benefits of recursion over iterative approaches
- useful for recursive data structures like trees and linked lists ⇒ can use recursion for querying or other operations
- Two parts to recursive function:
    - Base Case ⇒ how function knows to stop executing
    - Recursive Case
        
        ```Java
        function countdown(i) {
            console.log(i)  if (i <= 1) {  // base case
                return;
            } else {     // recursive case
                countdown(i - 1);
            }
        }
        
        countdown(5);    // This is the initial call to the function.
        ```
        
        ![[/Untitled 25.png|Untitled 25.png]]
        

The Call Stack

- recursive functions add function calls to the call stack and then return once the base case is reached
- E.g. factorials
    
    ```Java
    function fact(x) {
        if (x == 1) {
            return 1;
        } else {
            return x * fact(x-1);
        }
    }
    
    fact(3)
    ```
    
    the call stack
    
    - each call to `fact` has its own version of `x`
    
    ![[/Untitled 1 14.png|Untitled 1 14.png]]
    
    ![[/Untitled 2 13.png|Untitled 2 13.png]]
    
    ![[/Untitled 3 13.png|Untitled 3 13.png]]
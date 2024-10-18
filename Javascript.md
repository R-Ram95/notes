---
tags:
  - Frontend
---
- Event Loop
    
    Order of execution:
    
    1) **Call Stack:** Tasks on the call stack
    
    2) **Microtask Queue:** if call stack is empty, all queued up microtasks are popped onto the call stack and executed
    
    3) **Macrotask Queue:** if both the call stack and microtask queue are empty, the event loop checks if there are tasks left on the macro task queue
    
      
    
    There are two types of queues in the Event Loop:
    
    - macro task queue (task queue)
        - e.g. Web Api (`setTimeout`, `setInterval`)
    - micro task queue
        - `Promise callback`, `proces.nextTick`
    
    The callback within `.then()`, `.catch()`, `.finally()` is added to the **microtask queue**
    
- Promises
    
    - ==Reference==
        
        [https://medium.com/@lydiahallie/javascript-visualized-promises-async-await-a3f1aad8a943](https://medium.com/@lydiahallie/javascript-visualized-promises-async-await-a3f1aad8a943)
        
    
    ![[/Untitled 32.png|Untitled 32.png]]
    
    ```JavaScript
    new Promise((resolve, reject) => {
    
    	try {
    		const data = /* Perform a task */
    		resolve(data)
    	} catch(e){
    		reject(new Error(e))
    	}
    })
    ```
    
    - constructor receives two callbacks, `resolve`, to be handle success, `reject`, to handle errors
    
      
    
    An object that contains a **status** `[[PromiseStatus]]` and a **value** `[[PromiseValue]]`
    
      
    
    Promise Status:
    
    - `fufilled` ⇒ promise has been `rejected`
    - `rejected` ⇒ promise has been `rejected`
    - `pending` ⇒ neither resolved nor rejected
    
      
    
    The **value** of a promise, the value of `[[PromiseValue]]`, is the value that we pass to `resolve`, or `reject`
    
      
    
    `.then()`
    
    - called _after_ a promise is `resolved`
    - result is a `Promise`
    - result of previous `.then()` is the argument of the next callback
    
    ![[/Untitled 1 19.png|Untitled 1 19.png]]
    
    `.catch()`
    
    - called _after_ a promise is `rejected`
    
    `.finally()`
    
    - _always_ gets call no matter if the promise is resolved or rejected
    
      
    
    `async/await`
    
    - syntactic sugar for promise.then
    - if an async function is called, it gets added to the call stack
    - all lines before `await` are added to the call stack
    - when `await` is encountered
        - value that is _awaited_ gets executed
        - the async function execution is suspended
        - the rest of the async, after `await`, is added to the microtask queue
        - the event loop jumps out of the async function execution and continues executing the code in the execution context
    - once the call stack is empty again, the event loop looks at the microtask queue
    - the async function gets popped back onto the callstack, and execution is resumed
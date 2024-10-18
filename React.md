---
tags:
  - Frontend
---
Controlled vs. Uncontrolled Input

Hooks

Custom Hooks vs Function

React Renders

Structuring a React App

Synchronizing with Effect

# Controlled vs. Uncontrolled Input

- Controlled = `value` is set by a prop
- Uncontrolled = `value` not set by prop ⇒ uses `ref`

```JavaScript
// Uncontrolled Input
class Form extends Component {
  handleSubmitClick = () => {
    const name = this._name.value;
  };

  render() {
    return (
      <div>
        <input 
						type="text" 
						ref={(input) => (this._name = input)} 
				/>
        <button onClick={this.handleSubmitClick}>Sign up</button>
      </div>
    );
  }
}

// Controlled Input
class Form extends Component {
  constructor() {
    super();
    this.state = {
      name: "",
    };
  }

  handleNameChange = (event) => {
    this.setState({ name: event.target.value });
  };

  render() {
    return (
      <div>
        <input type="text" value={this.state.name} onChange={this.handleNameChange} />
      </div>
    );
  }
}
```

# Hooks

useMemo: Returns and stores the calculated value of a function in a variable ⇒ ==returns memoised value==  
useCallBack: Returns and stores the actual function itself in a variable ⇒ returns  
==memoised function==

> [!info] Understanding useMemo and useCallback  
> What's the deal with these two hooks?  
> [https://www.joshwcomeau.com/react/usememo-and-usecallback/](https://www.joshwcomeau.com/react/usememo-and-usecallback/)  

  

# Custom Hooks vs Function

Function if:

1. Doesn't depend on any values inside React ecosytem
2. Doesn't 'update' based on input, you call it once and use the return value

  

# React Renders

## Mental Model

$view = function(state)$﻿

- view is a function of state
- view is derived from state
- view changes when state changes

React creates a snap shot of our component ⇒ snapshot is composed of:

- props
- state
- event handlers
- description of ui

React takes this snapshot and uses it to update the view:

![[/Untitled 36.png|Untitled 36.png]]

![[/Untitled 1 22.png|Untitled 1 22.png]]

![[/Untitled 2 18.png|Untitled 2 18.png]]

## When do re-renders happen?

React will **only** re-render when **state changes**

- `event handler` invoked ⇒ calls `useState`'s updater function
- react checks that new state is different from snapshot
- triggers re-render

Whenever state is changed, children components will re-render as well:  
  

```JavaScript
const greetings = ['Hello', "Hola", "Bonjour"]

  const handleClick = () => {
    const nextIndex = index === greetings.length - 1
      ? 0
      : index + 1
    setIndex(nextIndex)
  }

  return (
    <main>
      <h1>{greetings[index]}, {name}</h1>
      <button onClick={handleClick}>
        Next Greeting
      </button>
      <Wave />
    </main>
  )
}

export default function App () {
  return <Greeting name="Tyler" />
}
```

The wave component will re-render every time index changes because `<Wave/>` is a child component of `Greeting`

![[/Untitled 3 18.png|Untitled 3 18.png]]

- React will only re-render child components if their props change IF the components are **pure** functions:
    
    - do not have internal state
    - produce the same output for the same input
    - receive data through their props
    - great use-case for memoization
    
    ## Pure vs. Impure function
    
    Pure functions produce the same output for the same input (based on props)
    
    Impure functions have side effects that change the output
    
    Example of a **Pure** Function:
    
    ```JavaScript
    function add(a+b) {
    	const total = a + b
    	return total;
    }
    ```
    
    - this function will return the same output for the same input, e.g `a=1` `b=2` will always return `3` if a and b are 1 and 2 respectively
    
    Example of **Impure** function:
    
    ```JavaScript
    function subtractWithRandom (a, b) {
    	const rnd = Math.random();
    	const total = a - b - rnd;
    	return total;
    }
    ```
    
    - we have a **Side Effect** where we change what total is randomly
    - does not provide same output for same input because we generate a random number in the function

## Escape hatches to break out of v=f(s) Paradigm

- `useRef`
- `useEffect`
- We cannot just assume that a component re-renders when it’s props change

## React Memo

- we have an expensive component that we want to re-render only if its props change
- `React.memo()`
    - input is component
    - output is component that re-renders only if its props change

## StrictMode

- react will re-render application an extra time
- only for development mode
- React will re-render twice if components are not pure

# Structuring a React App

> [!info] Structure Your React Apps Like It’s 2030  
> Every React Developer meets one issue during his or her journey.  
> [https://blog.devgenius.io/structure-your-react-apps-like-its-2030-aef02097cb3](https://blog.devgenius.io/structure-your-react-apps-like-its-2030-aef02097cb3)  

  

# Synchronizing with Effect

Two types of logic in react:

1) Rendering Code

- where you take props and state, transform them, and return JSX
- must be **pure** (do not modify state) ⇒ happens during rendering

2) Event Handlers

- contain side effects caused by user actions
- e.g. update input field or submit HTTP POST

**Effects**

- Let you specify effects that are caused by rendering itself rather than a particular event. e.g. making network calls
- Effects run at the end of a commit after the screen updates

  

**The useEffect Hook**

- used to sync external systems (web browser api, non-react components, etc.) with React
- useEffect “delays” a piece of code from running until that render is reflected on the screen
- _the behaviour of_ `useEffect` _is different without a dependency array and with an empty [] dependency array:_
    - without → runs after every render
    - with [] → runs only on mount (when component appears)
    - with [foo, bar] → runs on mount and also if either foo or bar changes

```JavaScript
useEffect(() => {
  // This runs after every render
});

useEffect(() => {
  // This runs only on mount (when the component appears)
}, []);

useEffect(() => {
  // This runs on mount *and also* if either a or b have changed since the last render
}, [a, b]);
```

  

> [!important]  
> what happens if we pass a ref into the dependency array?function VideoPlayer({ src, isPlaying }) {  
const ref = useRef(null);  
useEffect(() => {  
if (isPlaying) {  
ref.current.play();  
} else {  
ref.current.pause();  
}  
}, [isPlaying, ref]);You don’t need to pass a ref in the dependency array because the ref object has a stable identity.the ref object for the same useRef call will be the same so it will never cause a re-run of useEffect by itselfthe same apply for the set call in useState ** one caveat **if the ref is passed as a prop from a parent, we should include it in the dependency array because we can’t know if the parent always passes the same ref object  

  

**useEffect Cleanup**

Example code is a chat app where we create a connection to the chat server when the component initially mounts

```JavaScript
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
  }, []);
  
  console.log("Connecting...")
  return <h1>Welcome to the chat!</h1>;
}
```

In development mode, using `React.StrictMode`, React remounts every component once immediately after its initial mount.We see the “Connecting…” message twice when we mount the App because we never close the connection when the component unmounts. To fix:

```JavaScript
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    
    // *** HERE *** //
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

- we return a cleanup function that is run from the Effect when the component unmounts (gets removed)
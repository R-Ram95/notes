---
tags:
  - Backend
  - Framework
---
synchronous - one operation must complete before the other starts

asynchronous - operations can run at the same time (multi-threading)

Node is a runtime environment for javascript outside of the browser

- single threaded event-driven execution environment
- all requests to server are run on the same thread
- non-blocking async functions are important because if a synchronous operation takes a long time to process, all other requests to the server will be blocked until it complete

# Routing

route functions - end the HTTP request-response cycle by returning a response the client

```Java
// Home page route
router.get('/', function(req, res) {
  res.send('Wiki home page');
});
```

This route handler uses a call back function called `function` which calls the `.send()` method

- callback function - function that is “called at the back” i.e. it is invoked after the first function finishes

# Middleware

[https://www.youtube.com/watch?v=lY6icfhap2o](https://www.youtube.com/watch?v=lY6icfhap2o)

middleware - anything that happens between the time server gets request, and then sends response

- `function (request, response, next)`, can modify request and response, calls `next()` to move on to next middleware
- can be used for error handling

[https://blog.webdevsimplified.com/2019-12/express-middleware-in-depth/](https://blog.webdevsimplified.com/2019-12/express-middleware-in-depth/)

# Handling Errors

- handled by middleware functions with four arguments: (err, req, res, next)

```Java
app.use(function(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

- call after all app.use() and route calls ⇒ must be last middleware in request handling process

# Creating Express App

## From Scratch

1) Create directory and nav to it

```Java
mkdir myapp
cd myapp
```

2) Create package.json. This command prompts you for a number of things, including the name and version of your application and the name of the initial entry point file (by default this is **index.js**).

```Java
npm init
```

If you display the **package.json** file (`cat package.json`), you will see the defaults that you accepted, ending with the license.

```Java
{
  "name": "myapp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

3) Install express in myapp directory

```Java
npm install express
```

The dependencies section of your **package.json**will now appear at the end of the **package.json** file and will include _Express_.

```Java
{
  "name": "myapp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1"
  }
}
```

4) To use the Express library you call the `require()` function in your index.js file to include it in your application. Create this file now, in the root of the "myapp" application directory, and give it the following contents:

```Java
const express = require('express') // import express module
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!')
});

// server listens on port 3000
app.listen(port, () => {
  console.log(`Example app listening on port ${port}!`)
});
```

5) Start server by calling node with script in command prompt

```Plain
>node index.js
Example app listening on port 3000
```

## With Express Generator

```Java
npm install express-generator -g
```

```Java
express helloworld // creates skeleton in new directory helloworld
```

  

# View Engine

Responsible for creating HTML from views and it is usually a mix between HTML and a programming language. A _template engine_ enables you to use static template files in your application. At runtime, the template engine replaces variables in a template file with actual values, and transforms the template into an HTML file sent to the client.

How to select:

- Time to productivity — If your team already has experience with a  
    templating language then it is likely they will be productive faster  
    using that language. If not, then you should consider the relative  
    learning curve for candidate templating engines.  
    
- Popularity and activity — Review the popularity of the engine and  
    whether it has an active community. It is important to be able to get  
    support when problems arise throughout the lifetime of the website.  
    
- Style — Some template engines use specific markup to indicate  
    inserted content within "ordinary" HTML, while others construct the HTML using a different syntax (for example, using indentation and block  
    names).  
    
- Performance/rendering time.
- Features — you should consider whether the engines you look at have the following features available:
    - Layout inheritance: Allows you to define a base template and  
        then "inherit" just the parts of it that you want to be different for a  
        particular page. This is typically a better approach than building  
        templates by including a number of required components or building a  
        template from scratch each time.  
        
    - "Include" support: Allows you to build up templates by including other templates.
    - Concise variable and loop control syntax.
    - Ability to filter variable values at template level (e.g. making variables upper-case, or formatting a date value).
    - Ability to generate output formats other than HTML (e.g. JSON or XML).
    - Support for asynchronous operations and streaming.
    - Client-side features. If a templating engine can be used on  
        the client this allows the possibility of having all or most of the  
        rendering done client-side.
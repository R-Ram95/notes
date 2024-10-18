---
Author: Rohinesh Ram
tags:
  - Frontend
  - Fundamentals
---
  

Terms

Static vs. Dynamic Imports

In-browser vs. Build-time modules

Module Loaders / Module Formats

Build Tools

# **Terms**

**parsed at runtime** **-** JS in script tag of HTML is parsed and executed by the browser

**resolved at runtime** - how modules are loaded and executed

**bare module import** - importing modules using a module specifier rather than a file path or URL:

```JavaScript
import { someMethod } from 'my-dep';
```

# Static vs. Dynamic Imports

**Static:**

```JavaScript
import { functionName } from "module-name";
```

- use `import` statement
- resolved and loaded during build time when script is parsed (not runtime)
- imported modules are bundled into the final output file (the final bundle)
    - i.e. dependent modules are part of the bundle
- part of module dependency graph

**Dynamic:**

```JavaScript
import("module-name")
  .then((module) => {
    // You can use module.exports or module.namedExports here.
  })
  .catch((error) => {
    // Handle any errors that occur during loading.
  });
```

  

- use the `import()` function
- load modules async at runtime rathern than when the script is parsed
- return a promise that resolves to the modules namespace object

**Steps for dynamic imports:**

- Code containing the dynamic import is parsed at runtime.
- When the import statement is reached, the browser or JavaScript engine starts resolving the module's dependencies.
- The module is fetched over the network if it's not already in the browser's cache.
- Once the module and its dependencies are loaded and ready, the promise returned by `**import()**` is resolved, and you can work with the imported module.

# In-browser vs. Build-time modules

In-browser: imports and exports are resolved within the browser ⇒ the browser makes separate network requests for each module

Build-time: imports and exports are bundled by the build tool ⇒ the browser makes one network request for the bundle

In-browser is important for single-spa because it allows applications to use the “global” imported or exported module

# Module Loaders / Module Formats

Understanding module formats:

[https://weblogs.asp.net/dixin/understanding-all-javascript-module-formats-and-tools#system-module-systemjs-module](https://weblogs.asp.net/dixin/understanding-all-javascript-module-formats-and-tools#system-module-systemjs-module)

**SystemJS**: SystemJS is a popular dynamic module loader for JavaScript. It is designed to work in both browsers and Node.js and supports asynchronous module loading and dynamic imports.

- import-maps are used as configuration for systemjs in the browser, it uses the configuration to determine where to get modules when using bare module specifiers
- There is a `System.register` provided by SystemJS

SystemJS docs

[https://github.com/systemjs/systemjs/blob/main/docs/system-register.md](https://github.com/systemjs/systemjs/blob/main/docs/system-register.md)

  

**CommonJS**: CommonJS is a module system used in Node.js. It uses the `**require**` function to load modules synchronously. CommonJS modules are commonly used on the server-side (node) of JavaScript applications.

  

**ES modules (ES6 modules)**: ES modules are the native module system introduced in ECMAScript 2015 (ES6). They are designed for both browser and server environments and provide a standard way to define and load modules using `**import**` and `**export**` statements.

- native means ES modules are an inherent part of the JS language and are supported by JS engines like the browser and Node.js

  

**AMD (Asynchronous Module Definition)**: AMD is a module format and loader primarily used in browsers. It emphasizes asynchronous module loading and is often used with libraries like RequireJS.

  

**UMD (Universal Module Definition)**: UMD is not a loader itself but a pattern for creating JavaScript modules that can work with various module systems, including CommonJS, AMD, and global variables.

  

**Webpack and Rollup**: These are popular module bundlers that can handle both CommonJS and ES modules, allowing developers to use various module systems while optimizing module loading for production.

# Build Tools

[[Vite]]
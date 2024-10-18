---
Author: Rohinesh Ram
Date: 2024-02-10
tags:
  - Frontend
---
**Table of Contents**

Microfrontend: single-spa

Single-spa vs. Module Federation

Setting up Single-Spa + Vite

MFE Architectures and Tools

Option 2: Vite + Mod-Fed + Mixing Frontend Frameworks

Intro

What is Module Federation and how can it be used for MFEs?

How does Module Federation Work?

Proof of Concept

Conclusion And Final Thoughts

# Microfrontend: single-spa

Single-spa is a **UI Architectural Style** ⇒ it allows you to load JS modules into your web-app at **Run Time** (when the user accesses your web-app). You deploy each MFE(i.e. each JS module) and load it in the browser at **run-time** - single-spa provides methods that allows us to do this.

> [!important]  
> Microfrontend Types as defined by single-spaApplicationsRender components for a set of specific routes - this is the key differentiation between applications and parcels. the root-config (orchestrator) uses the single-spa.registerApplication api to define applications ahead of time. It also defines the activityFunction which determines when (which url routes, e.g. <url>/home-page) applications should be mounted (rendered in the DOM)single-spa manages the lifecycles of the applications, i.e. when they should be bootstrapped, mounted, and unmounted ⇒ loaded for the first time, added to the DOM when the user navigates to the respective route, or removed from the DOM when they user navigates away from the respective route, respectively.applications export their lifecycle methods (bootstrap(), mount(), unmount()) so that single-spa can manage themParcelsParcels are reusable pieces of UI that can be used across many applications ⇒ they are not tied for any particular url route, but rather they can be used by many applications that are mounted at multiple url routesSingle-spa does not manage the lifecycle of parcels ⇒ you need to call the mountParcel or mountRootParcel apis when you want to use a UI component from a parcel and then call the unmount method on the parcel when the component that mounts the parcel, unmountsSingle-spa recommends using Parcels for sharing pieces of UI between different frameworks, but should not be used when sharing UI between the same frameworkUtilitiesa way to share logic (functions) with multiple Applications or ParcelsUtilities do not have UI piecesexamples of utility MFEsNotification serviceStyleguide/component libraryError tracking serviceAuthorization service ⇒ define commons methods to retrieve a JWT from the server for exampleData fetching ⇒ define common methods for getting data  

  

# Single-spa vs. Module Federation

Single-spa is a UI architecture → it defines how we should layout our shell application as well as the micro-front-end applications. It also manages the lifecycle of applications such as when and how they are bootstrapped (loading into the DOM for the first time), mounted (how they are added to the DOM for the second time and thereafter), and unmounted (how they are removed from the DOM). Single-spa relies on SystemJS which is a **module loader** (the thing that runs in the browser and loads our micro-frontend bundles into the browser at **runtime**)

Module-federation is a code transport layer → it is like a pipeline between applications which allows us to share code (javascript) between applications at runtime. It does not inherently manage the lifecycles of applications as single-spa does, we need to do this ourselves. Module-federation is also a two-way street - code can be shared both ways between applications. For example, Application A can share components with Application B and vice versa.

It is important to note that Single-spa and Module Federation are not mutually exclusive, i.e. they can be used in combination with one another. Module Federation can be used to share **any** javascript, for example, helper functions for authentication.

# Setting up Single-Spa + Vite

- Setting up dev server:
    - default behaviour is to use ES6 Modules
    - should use source-maps
    - `import` allows for dynamic loading of modules
    - input to [`registerApplication.app`](http://registerApplication.app) should be entry file of MFE
- production bundle:
    - input to [`registerApplication.app`](http://registerApplication.app) should be the production bundle
        - can use `import`

2 options to build single-spa with vite

1) use `npx create-single-spa` ⇒ use this to create Root-Config, Applications, Utility, Parcels but it uses Webpack. We would need to convert the repo to use Vite

2) use `npm create vite@latest <APP_NAME> -- --template vanilla-ts` then add the necessary single-spa packages

==**Could create a cli to set this up**==

- Option1> [!important]  
    > Option 1 - Using Single-Spa CLI Tool: Orchestrator1) Bootstrap a single-spa root-confignpx create-single-spa --moduleType root-configfollow the promptscreates a webpack project with the single-spa dependencies in the index.ejs (but we won’t need these)2) Add Vitenpm i --save-dev vite3) Update Package.jsonadd"type": "module"add "build": "vite build",  
    "dev": "vite serve",4) Create vite.configimport { defineConfig } from 'vite';  
      
    export default defineConfig({  
    root: 'src',  
    server: {  
    port: 9000, // This is optional (defaults to 5173)  
    },  
    build: {  
    target: "es2022",  
    rollupOptions: {  
    input: ["./src/<NAME_OF_ENTRY_FILE>"],  
    preserveEntrySignatures: "exports-only",  
    output: {  
    format: "system", // when we build, make sure the output module is of the SystemJS type  
    exports: "auto",  
    entryFileNames: "[name].js", // Makes sure that the output file name is consistent  
    },  
    },  
    },  
    });5) Create in index.html in the root<!DOCTYPE html>  
    <html lang="en">  
    <head>  
    <meta charset="UTF-8" />  
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />  
    <title>Document</title>  
      
    <!-- get systemjs -->  
    <script src="https://cdn.jsdelivr.net/npm/systemjs@6.8.3/dist/system.js"></script>  
    <script src="https://cdn.jsdelivr.net/npm/systemjs@6.8.3/dist/extras/amd.js"></script>  
    <script src="https://cdn.jsdelivr.net/npm/regenerator-runtime@0.13.7/runtime.min.js"></script>  
    <script type="systemjs-importmap" src="./import-map.json"></script>  
    <script  
    type="text/javascript"  
    src="https://cdn.jsdelivr.net/npm/import-map-overrides/dist/import-map-overrides.js"  
    ></script>  
    <import-map-overrides-full  
    show-when-local-storage="devtools"  
    ></import-map-overrides-full>  
    </head>  
    <body>  
    <!-- THIS SCRIPT OVERRIDE THE System.import() so that Vite can work in local dev !-->  
    <script type="module">  
    (function (global) {  
    const systemJSPrototype = global.System.constructor.prototype;  
      
    const originalCreateScript = systemJSPrototype.createScript;  
    systemJSPrototype.createScript = function () {  
    return Object.assign(originalCreateScript.apply(this, arguments), {  
    type: "module",  
    });  
    };  
      
    // override SystemJS behaviour when dynamically importing  
    const originalInstantiate = systemJSPrototype.instantiate;  
    systemJSPrototype.instantiate = function (url) {  
    const isEsm = url.includes(".ts");  
      
    return originalInstantiate.apply(this, arguments).then((register) => {  
    // use native import for ESM modules  
    if (isEsm) {  
    return import(/* @vite-ignore */ url).then((m) => [  
    [],  
    (_export) => (_export(m), {}),  
    ]);  
    }  
      
    // default to using SystemJS for non ESM modules (likely the system type)  
    return register;  
    });  
    };  
    })(typeof self !== "undefined" ? self : global);  
    </script>  
    <script type="module">  
    System.import("THE_NAME_PROPERTY_IN_package.json");  
    </script>  
    </body>  
    </html>6) Create an import-mapin the root create an import-map.json{  
    "imports": {  
    "single-spa": "https://cdn.jsdelivr.net/npm/single-spa@5.9.0/lib/system/single-spa.min.js",  
    "@mfe-single-spa/root-config": "http://localhost:9000/<PATH_TO_ENTRY_FILE.ts>",  
    ... add other MFES here  
    }  
    }7) Remove Webpack and Babeldelete webpack.config.jsdelete babel.config.jsondelete index.ejsuninstall dependencies"@babel/core": "^7.15.0","@babel/eslint-parser": "^7.15.0","@babel/plugin-transform-runtime": "^7.15.0","@babel/preset-env": "^7.15.0","@babel/runtime": "^7.15.3","@babel/preset-typescript": "^7.15.0","html-webpack-plugin": "^5.3.2","webpack": "^5.75.0","webpack-cli": "^4.8.0","webpack-dev-server": "^4.0.0","webpack-merge": "^5.8.0","webpack-config-single-spa-ts": "^4.0.0","@types/webpack-env": "^1.16.2”8) Replace tsconfig.json{  
    "files": ["src/NAME_OF_ENTRY_FILE"],  
    "compilerOptions": {  
    "esModuleInterop": true,  
    "module": "esnext",  
    "allowSyntheticDefaultImports": true,  
    "moduleResolution": "node",  
    "resolveJsonModule": true,  
    "lib": [  
    "dom",  
    "es5",  
    "scripthost",  
    "es2015",  
    "es2015.promise",  
    "es2016.array.include",  
    "es2018"  
    ]  
    },  
    "include": ["src/**/*", "./vite.config.ts"],  
    "exclude": ["node_modules"]  
    }  
      
    > [!important]  
    > Option 1 - Using Single-Spa CLI Tool: Application1) Bootstrap a single-spa application or parcelnpx create-single-spa --moduleType app-parcelfollow the promptscreates a webpack project with the single-spa dependencies in the index.ejs (but we won’t need these)2) Add Vitenpm i --save-dev vite3) Update Package.jsonadd"type": "module"add "build": "vite build",  
    "dev": "vite",4) Add vite.config.tsimport { defineConfig } from "vite";  
      
    export default defineConfig({  
    server: {  
    port: 9001, # set to port you want app to run on locally  
    cors: true,  
    origin: "http://localhost:<PORT_FROM_ABOVE>", # this is so assets are served properly  
    },  
    build: {  
    rollupOptions: {  
    input: "src/<ENTRY_FILE>",  
    output: {  
    format: "systemjs",  
    },  
    },  
    },  
    });4) Remove webpack and babelincludes all dependencies from package.json  
    
      
    

  

**Problems encountered and their solutions**

> [!important]  
> Problem 1: Serving AssetsProblem:The assets from the MFE were not rendered while using the dev server because the url path of the assets were using the base url of the single-spa config repo and not the MFE. (localhost:9000 instead of localhost:9002 for example)Article and GH discussions talking about the issue:https://github.com/vitejs/vite/discussions/13927https://github.com/vitejs/vite/issues/14000https://webjose.hashnode.dev/vite-plugin-single-spa-vite-projects-made-single-spa-compatible-with-one-plug-inIn summary:Vite base config option gets stripped down to just / instead of http://localhost:<mfe port> due to a regex expression in the vite source code in serve mode (dev)this is a big deal because we need the full URL of the assets from the MFE in the config-appNOTE: base is the base public path when served in dev or production as per the Vite docsExample of problem:root-config is Vite served on http://localhost:9000MFE is Vite with React served on http://localhost:9002Console Errors:![[/Untitled 42.png|Untitled 42.png]]assets are not found, note they are said to be from 9000 but should be from 9002HTML in browser:![[/Untitled 1 27.png|Untitled 1 27.png]]Note how the asset urls are relative (start with /)Solution:Use the Vite server.origin property in the config and specify the URL of the MFE (i.e. http://localhost:9002)server.origin defines the origin of generated asset URLs during dev (see Vite docs)vite.config.ts - of the application (not the root-config)import { defineConfig } from "vite";  
  
export default defineConfig({  
server: {  
port: 9002,  
cors: true,  
origin: "http://localhost:9002",  
},  
build: {  
rollupOptions: {  
input: "src/xerris-auth-app.ts",  
output: {  
format: "systemjs",  
},  
},  
},  
});Browser HTML becomes this after using the server.origin property:![[/Untitled 2 22.png|Untitled 2 22.png]]Conclusion:Use the server.origin property in the vite.config of each application and set it to the development url which ensures that assets are served from the correct url.  
  
> [!important]  
> Problem 2: Using Import-mapsProblem:We want to be able to add import-maps to the HTML of out config repo to resolve the bare-module specifiers of our MFEs to their full URL packages for cleaner code. E.g.![[/Untitled 3 22.png|Untitled 3 22.png]]Note the script type='systemjs-importmap', this is specific to system-js importmaps. The above lets us use bare-module specifiers in our code like this:![[/Untitled 4 15.png|Untitled 4 15.png]]In this context, it is used by single-spa to register our MFE from the orchestrator app. Note how we are using System.import() which is the SystemJS way of dynamically loading modules.The problem occurs when we use Vite as the builder for our MFE because Vite is built specifically to work with ES6 modules, and can optimize the modules efficiently during dev and build. While System.import() can be used to dynamically load ES6 modules, it introduces extra complexity and kills the optimization of using Vite to dynamically load modules. Solution:Because we are using Vite, we can (and should) use import() instead of System.import() to take advantage of Vite’s optimizations. Using System.import() with Vite bypases optimizations and introduces unncessary complexity⭐System.import vs. importSystem.import() is part of the SystemJS module loader which is used to dynamically load modules. It is an older module loading system that predates ES6 (ECMAScript) modules. You can tell if the package is SystemJS if it includes System.register(…) in the built package.import() is part of the ES6 module system (native) and is associated with the export and import keywordsThe index.html of our config repo becomes this:![[/Untitled 5 13.png|Untitled 5 13.png]]And to register our MFE using single-spa in our config repo we use:![[/Untitled 6 10.png|Untitled 6 10.png]]Note how we use type='importmap' instead of type='systemjs-importmap' and import(<bare-module-specifier>) instead of System.import()because we are no using SystemJS.❓Another ProblemI was trying to use the system-importmap type in the index.html to load the import map but faced an issue where the browser could not resolve the bare-module specifier when using import() to load the module. I am not sure why this happened, maybe a compatability issue between systemjs and the import() function.Conclusion:Don’t use SystemJS when using Vite in development because we are throwing away optimizations made by Vite.  
  
> [!important]  
> Problem 3: Native ESM and SystemJSProblem:Issue: not being able to use import when using System.import() (Native ESM with System.import() ???? )https://github.com/single-spa/single-spa/issues/1049Solution:Override the behaviour of System.import() to use the import() or System.import() methods based on whether the module is a SystemJS or an ESM module formatAdd this script to the index.html of the orchestrator (root-config)  
(function (global) {  
const systemJSPrototype = global.System.constructor.prototype;  
  
const originalCreateScript = systemJSPrototype.createScript;  
systemJSPrototype.createScript = function () {  
return Object.assign(originalCreateScript.apply(this, arguments), {  
type: "module",  
});  
};  
  
// override SystemJS behaviour when dynamically importing  
const originalInstantiate = systemJSPrototype.instantiate;  
systemJSPrototype.instantiate = function (url) {  
const isDev = url.includes(".ts");  
  
return originalInstantiate.apply(this, arguments).then((register) => {  
// use native import for ESM modules  
if (isDev) {  
return import(/* @vite-ignore */ url).then((m) => [  
[],  
(_export) => (_export(m), {}),  
]);  
}  
  
// default to using SystemJS for non ESM modules (likely the system type)  
return register;  
});  
};  
})(typeof self !== "undefined" ? self : global);Conclusion:Adding the script chooses how to bring in the files (using SystemJS or the browser) into our application. During dev, we use the Browser and can leverage optimizations made by Vite. During Production, we need to use SystemJS because we are loading in packaged JS modules.  

  

# MFE Architectures and Tools

Option 1: single-spa + vite

Option 2: mod-fed + vite

Option 3: Astro + vite

Option 3b: Astro + mod-fed + vite

Option 4: single-spa + mod-fed + webpack

Option 4b: single-spa + mod-fed + vite

# Option 2: Vite + Mod-Fed + Mixing Frontend Frameworks

## Intro

The goal of this is to explore alternative architectures to that proposed by Single-spa. Some hard goals for this exploration are to:

- create a shell application which will load the other MFEs
- create 3 MFEs, one for each of React, Vue, and Angular
- do the orchestration of MFEs in the Shell app using Vite and Vite Module Federation

And one soft goal is to use Vite as the module bundler/builder because Vite helps with good DX and now supports module federation via the originjs/vite-module federation plugin.

## What is Module Federation and how can it be used for MFEs?

Module Federation is a code-transport layer - it is like a pipeline between applications which allows us to share code (javascript) between applications at runtime. It does not inherently manage the lifecycles of applications as single-spa does, we need to do this ourselves. Module-federation is also a two-way street - code can be shared both ways between applications. For example, Application A can share code with Application B and vice versa. Module federation can be used to load application code into a shell, and then these applications can be added to the DOM as needed using routing (react-router for react) in the shell application.

Single-spa is a UI architecture - it defines how we should layout our shell (aka root-config in the single-spa world) application as well as the micro-front-end applications. It also manages the lifecycle of applications such as when and how they are bootstrapped, mounted (how they are added to the DOM), and unmounted (how they are removed from the DOM). Single-spa relies on SystemJS which is a **module loader** (code that runs in the browser to load our micro-frontend bundles).

It is important to note that Single-spa and Module Federation are not mutually exclusive, i.e. they can be used in combination with one another. Module Federation can be used to share **any** javascript, for example, helper functions for authentication.

## How does Module Federation Work?

The goal of this example is to show how we can share Javascript between two applications using module federation. We will create two vite projects, one called Host and one called Remote - Remote will share a React component with Host.

- create two vite projects using the `create-vite` cli tool, call one Host and the other Remote
- make both projects React - though they do not need to be
- add the Vite Module Federation plugin from originjs: `npm i` `@originjs/vite-plugin-federation --save-dev` to both projects
- Create a Button component in Remote:
    
    ```JavaScript
    import { useState } from "react";
    
    const Button = () => {
      const [count, setCount] = useState(0);
      return (
        <div>
          <button onClick={() => setCount(() => count + 1)}>
            Click me: {count}
          </button>
        </div>
      );
    };
    
    export default Button;
    ```
    

  

We need to expose this Button component so that it can be shared with the Host and this is done in the `vite.config.ts` of the Remote application.

- `name` - name of the module we are exposing
- `filename` - name of the file that will be the entry point for our Host application to get this component, more on this later
- `exposes` - mapping between component name and where the source code is. This name will be used on the Host side to import our button
- `shared` - any thing we know will be used by the host as well (since our host uses react, we can add react here)
- `build` ⇒ settings that Vite uses during build time. This part is important and we will circle back.
    
    ```JavaScript
    import { defineConfig } from "vite";
    import react from "@vitejs/plugin-react";
    import federation from "@originjs/vite-plugin-federation";
    
    // https://vitejs.dev/config/
    export default defineConfig({
      plugins: [
        react(),
        federation({
          name: "remote",
          filename: "remoteEntry.js",
          exposes: {
            "./Button": "./src/components/Button",
          },
          shared: ["react", "react-dom"],
        }),
      ],
      build: {
        modulePreload: false,
        target: "esnext",
        minify: false,
        cssCodeSplit: false,
      },
    });
    ```
    

  

If you're familiar with Vite, you might typically use `**npm run dev**` or `**npx vite**` to start the development server. However, in this particular scenario, these commands won't address our needs. Instead, we need to build and preview the project locally using `npm run build` or `npx vite build` and `npm run preview` or `npx vite preview`. We need to do this because the Vite Module Federation plugin exposes our button through the `remoteEntry.js` file which is generated on `vite build`. When we run `vite build` we should see these generated files in the `dist/assets` folder:

![[/Untitled 7 7.png|Untitled 7 7.png]]

  

The `remoteEntry.js` file is a mapping between a component name and the module where the code for the component is located. Here is a snippet from our `remoteEntry.js` (as long as `build.minify` is set to `false` in the `vite.config.ts`):

  

```JavaScript
let moduleMap = {
"./Button":()=>{
      dynamicLoadingCss(["style-Dd-7Bgej.css"], false, './Button');
      return __federation_import('./__federation_expose_Button-BLvLT2W3.js').then(module =>Object.keys(module).every(item => exportSet.has(item)) ? () => module.default : () => module)},};
```

  

We see the `./Button` is mapped to `./__federation_expose_Button-BLvLT2W3.js` if we open this file up we can see this which is indeed the code for out Button:

```JavaScript
const {useState} = await importShared('react');

const Button = () => {
  const [count, setCount] = useState(0);
  return /* @__PURE__ */ jsxRuntimeExports.jsxs("div", { children: [
    /* @__PURE__ */ jsxRuntimeExports.jsxs("button", { onClick: () => setCount(() => count + 1), children: [
      "Click me: ",
      count
    ] }),
    /* @__PURE__ */ jsxRuntimeExports.jsx("img", { src: "./react-icon.png", alt: "" })
  ] });
};

export { Button as default, jsxRuntimeExports as j };
```

  

Now, we need to get the button from our Remote into the Host. To do this, we add the following to the `vite.config.ts` in the host:

```JavaScript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import federation from "@originjs/vite-plugin-federation";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    react(),
    federation({
      name: "host",
      remotes: {
        remote: "http://localhost:3001/assets/remoteEntry.js",
      },
      shared: ["react", "react-dom"],
    }),
  ],
});
```

- `remotes` - a mapping between a qualified name and the javascript we are bringing into host. Note that it is the `remoteEntry.js` file that is built and served by the Remote application
- `shared` - the shared libraries between both projects

  

Finally we can add the button to our `App.jsx` in the host. Note how we import the button using a combination of the key in the remotes section of the Host application Vite config and the key in the exposes section of the Remote application Vite config

```JavaScript
import { useState } from "react";
import reactLogo from "./assets/react.svg";
import viteLogo from "/vite.svg";
import "./App.css";
// import from Remote
import Button from "remote/Button";

function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <div>
        <a href="https://vitejs.dev" target="_blank">
          <img src={viteLogo} className="logo" alt="Vite logo" />
        </a>
        <a href="https://react.dev" target="_blank">
          <img src={reactLogo} className="logo react" alt="React logo" />
        </a>
      </div>
      <h1>Vite + React</h1>
      <div className="card">
        <button onClick={() => setCount((count) => count + 1)}>
          count is {count}
        </button>
        <p>
          Edit <code>src/App.tsx</code> and save to test HMR
        </p>
      </div>

      <h1>Host</h1>
      <div className="dynamic-div">
        <h2>From Remote:</h2>
        <Button />
      </div>

      <p className="read-the-docs">
        Click on the Vite and React logos to learn more
      </p>
    </>
  );
}

export default App;
```

  

To run the project, we need to run `npm run dev` in the host and `npm run build` then `npm run preview` in the remote and we see this:

![[/Untitled 8 6.png|Untitled 8 6.png]]

## Proof of Concept

Now that we understand module federation, let us put it into practice to build out an MFE architecture without single-spa and SystemJS.

Our goals for the POC are to:

- create an MFE architecture using Module Federation as opposed to Single-Spa (though they do not need to be used separately)
- Mix front-end frameworks - we want React, Angular and Vue projects loaded into a single shell application

One of the nice things that Single-spa does for us is manage the lifecycles of MFE applications in the root-config (shell) via the Lifecycle methods (bootstrap, mount, and unmount) which define **how** applications are added to the DOM and activity functions which decide **when** the applications are rendered to the user. Our POC will need to do the same. Let us deal with the **when** first.

The way we will achieve this is by using React Router in our shell application. This will work by having routes for each of the React, Vue and Angular applications. When the browser is on a route, we simply load that applications code into the browser and render it onto the DOM. For example when we are on the `/react` route we will load the code from `App.jsx` into the Browser. Here is a diagram illustrating this. Also, note that our shell application will need to be a React app if we choose to the react router.

![[/Untitled 9 4.png|Untitled 9 4.png]]

Now that we’ve handled when applications are mounted, lets deal with how. An important factor to consider is that different frameworks have different ways of mounting their components to the DOM. For example React does the following where we create an element in the `index.html` with the `className=root` and we mount the application onto it using the `ReactDom.render()` method.

  

```JavaScript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

  

Whereas Vue does the following where we create an element with the `id=app` and we mount our Vue application to it using the `Vue.mount()` method

```JavaScript
import Vue from 'vue';
import App from './App.vue';

new Vue({
  render: h => h(App)
}).$mount('\#app');
```

  

So, how do we mount these applications at Runtime in the shell? We have two options:

1) Web components and more specifically, customElements

2) Custom Shims

  

Custom Elements enable us to create our own HTML elements with custom behaviour. This allows us to define new types of HTML elements, encapsulate functionality, and reuse them in our web applications. Here is an example of a customElement:

```JavaScript
class MyComponent extends HTMLElement {
  constructor() {
    super();
    // Custom element initialization code
  }
}

customElements.define('my-component', MyComponent);
```

And then we can use it in our html like so:

```JavaScript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Custom Element Example</title>
  <script src="path-to-javascript/file.js" defer></script>
</head>
<body>

  <my-component></my-component>

</body>
</html>
```

For our purposes, we would simply wrap our component in a customElement and then add the html tag to our `index.html` of the shell application. This is a great approach to solving the **how** problem but for this POC we will go with option 2, writing custom shims. Essentially, we will be writing `mount` methods in each project and expose them (via module federation) to the shell application. The shell application will create html elements and then call the mount method from each project to attach it to the created elements. Lets look at some code:

In the entry file of our Vue app we have

```JavaScript
import "./style.css";
import { createApp } from "vue";
import App from "./App.vue";

const mount = (el) => {
  const app = createApp(App);
  app.mount(el);
};

export { mount };
```

which takes in an HTML element as an argument and calls the Vue `app.mount()` function on it. Lets see how the shell application uses this `mount` function:  
  
In the  
`Vue-shim.jsx` component of the shell application we have:  
  

```JavaScript
import { mount } from "vueApp/App";
import { useEffect, useRef } from "react";

const VueApp = () => {
  const ref = useRef(null);

  useEffect(() => {
    mount(ref.current);
  }, []);

  return <div ref={ref} />;
};

export default VueApp;
```

  

We create a ref, a reference to an HTML element, in React. We then import the `mount` function defined in the Vue project (via module federation) and call it with the element we created as an argument. We have successfully mounted our Vue application in our React Shell. Finally, in the `App.jsx` file of the shell, we have our router which render the `VueApp` component when the `/vue` route is hit. We write similar functions and follow a similar pattern for React and Angular.  
  

```JavaScript
import "./App.css";
import { BrowserRouter, Route, Routes } from "react-router-dom";
import VueApp from "./components/VueApp-shim";
import AngularApp from "./components/AngularApp-shim";
import Dashboard from "./components/Dashboard/Dashboard";
import ReactApp from "reactApp/App";
import Navbar from "./components/Navbar";

function App() {
  return (
    <BrowserRouter>
      <Navbar />
      <Routes>
        <Route path="/" element={<Dashboard />} />
        <Route path="react" element={<ReactApp />} />
        <Route path="vue" element={<VueApp />} />
        <Route path="angular" element={<AngularApp />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

  

Here’s a video of the final project:

![[Video_to_GIF_converter.gif]]

  

## Conclusion And Final Thoughts

We created a Microfrontend architecture which is not dependent on single-spa and which mixes front end frameworks. The complete source code can be found here:

https://github.com/xerris/mfe-vite-mod-fed

  

While Vite Module Federation is interesting and viable, it presents certain limitations in its support for dev mode in remote applications due to the necessity of building and serving the `**remoteEntry.js**` file, resulting in the absence of Hot Module Replacement (HMR) during development. While the host application (Shell) can run in dev mode, challenges arise with asset sharing, particularly for images. Vite's rewriting mechanism and removal of base URLs from image src tags make it impractical to share assets from the remote to the dev application. For instance, if an image resides in a remote application served on `**localhost:3001**` while the host application operates on `**[localhost:3000](http://localhost:3000)**`, Vite will alter the image URLs on 3001 to reference `**http://localhost:3000/path-to-image.jpeg**`. Additionally, the absence of import-maps support means that all modules must be resolved at build-time rather than run-time, thereby limiting the dynamic benefits of import mapping.
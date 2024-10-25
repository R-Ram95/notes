
- **Nutrien HomeSafe Code Review**> [!important]  
    > orchestrator Approot-config.tsregisters applications and passes custom props to each applicationchannel - used for communication between MFEs using Postal isSidebarOpen - boolean to check if the side bar is openauthenticatedFetch: authenticatedFetchWithGroupId isAuthenticated - not sure what this is for, it is always set to trueauthConfig - used by sidebar and dashboard repos in the auth.ts file where a bunch of authentication methods are definedsetAggregateGroupId - not used anywhere but defined in orchestrator, not sure why it is passed to all appsgetAggregateGroupId - used only in the orchestrator app, not sure why it is passed to all appsgetAllUserPermissions - used only in the orchestrator app, not sure why it is passed to all appssetUserPermissions - used only in the orchestrator app, not sure why it is passed to all appsuserHasPermission - used in sidebar app to see if user is allowed to navigate to a route  
      
    > [!important]  
    > How does messaging between MFEs work?using a JS88 library called Postalit is a pub/sub type of messaging but has a request/response type extension’https://github.com/postaljs/postal.jsroot-config.ts - orchestrator repo// Orchestrator: root-config.ts  
    // create the channel for communication  
    const channel = postal.channel("main");  
    let sidebarOpen = false;  
      
    // Check if object is a type of ToggleEventData  
    const instanceOfToggleEventData = (obj: any): obj is ToggleEventData => {  
    return "open" in obj;  
    };  
      
    // Monitor Channel for sidebar toggle events  
    channel.subscribe("sidebar.toggle", data => {  
    if (instanceOfToggleEventData(data)) {  
    sidebarOpen = data.open;  
    }  
    });  
      
    const isSidebarOpen = () => sidebarOpen;  
      
    registerApplication(  
    config.appName,  
    () => System.import(config.moduleId),  
    ACTIVITY FUNCTION NOT SHOWN  
    },  
    {  
    // pass the communication channel to the other apps as a Custom Prop  
    channel,  
    // pass the isSidebarOpen to the other apps  
    isSidebarOpen,  
    isAuthenticated: true,  
    authConfig,  
    authenticatedFetch: authenticatedFetchWithGroupId,  
    setAggregateGroupId,  
    getAggregateGroupId,  
    getAllUserPermissions,  
    setUserPermissions,  
    userHasPermission  
    }  
    );  
      
    > [!important]  
    > How are API Calls made?All api calls are made through the orchestratorthe root-config.ts of the orchestrator defines the authenticatedFetch functionadds things needed for request (endpoint url, headers, etc.) this function is passed to the applications using CustomProps (passed to each application during the lifecycle methods) of the single-spa registerApplication() methodactual api calls are made using methods defined in the @nutrien/cxp-api-fetch-module packageauthenticatedFetch// adds  
    const authenticatedFetchWithGroupId = (  
    input: RequestInfo,  
    init?: RequestInit | undefined,  
    cacheBehaviour: "cached" | "ignoreCache" | "notCached" = "cached"  
    ) => {  
    if (!aggregateGroupId) {  
    getAggregateGroupId();  
    }  
      
    if (aggregateGroupId) {  
    init = {  
    ...init,  
    headers: {  
    ...init.headers,  
    "x-aggregateGroupId": aggregateGroupId.toString()  
    }  
    };  
    }  
      
    // functions for making api calls are defined in @nutrien/cxp-api-fetch-module  
    switch (cacheBehaviour) {  
    case "cached":  
    return cachedAuthenticatedFetch(input, init, false, true); // defined in @nutrin/cxp-api-fetch-module  
    case "ignoreCache":  
    return cachedAuthenticatedFetch(input, init, true, true);  
    case "notCached":  
    return authenticatedFetch(input, init);  
      
    default:  
    throw new Error(  
    "Incorrect Api configuration for 'cacheBehaviour', please use one of the following: 'cache' | 'ignoreCache' | 'notCached'"  
    );  
    }  
    };  
    each application defines a callAPI method which uses the orchestrator.authenticatedFetch to make callscallApi - defined in she-auth-app repoexport const callAPI = async (  
    endpoint: string,  
    requestInit?: RequestInit | undefined,  
    cacheBehaviour: "cached" | "ignoreCache" | "notCached" = "cached"  
    ) => {  
    try {  
    if (DEBUG) console.log(endpoint, requestInit);  
    // USES ORCHESTRATOR TO MAKE THE CALL  
    const response = await orchestrator.authenticatedFetch(  
    endpoint,  
    requestInit,  
    cacheBehaviour  
    );  
    if (DEBUG) console.log(endpoint, response);  
      
    const data = await response.json();  
    if (DEBUG) console.log(endpoint, data);  
    if (data.success || data.successful || response.ok) {  
    return data;  
    }  
    throw new Error(data.errorMessage);  
    } catch (error) {  
    if (DEBUG) console.log(endpoint, error);  
      
    if (isPromise(error)) {  
    const result: any = await error;  
    if (DEBUG) console.log(endpoint, result);  
    if ("errorMessage" in result) throw new Error(result.errorMessage);  
    }  
      
    throw error;  
    }  
    };where does orchestrator come from in the code above?from index.tsx where we define the required single-spa life-cycle methods (bootstrap, mount, unmount)note the props passed into the mount() method, these are the props we passed in to the registerApplication() function in the orchestrator-app repoin the she-dashboard repo, the props are stored in a window object which is not idealindex.tsximport React from "react";  
    import ReactDOM from "react-dom";  
    import singleSpaReact from "single-spa-react";  
    import Root from "./components/Root";  
    import { ICXPOrchestrator } from "@nutrien/cxp-orchestrator-module/build/interfaces";  
      
    const lifecycles = singleSpaReact({  
    React,  
    ReactDOM,  
    rootComponent: Root,  
    errorBoundary() {  
    return <div>This renders when a catastrophic error occurs</div>;  
    },  
    suppressComponentDidCatchWarning: true  
    });  
      
    export const bootstrap = lifecycles.bootstrap;  
    export let orchestrator: ICXPOrchestrator;  
    export async function mount(props: any) {  
    orchestrator = props as ICXPOrchestrator;  
    return lifecycles.mount(props);  
    }  
    export const unmount = lifecycles.unmount;  
      
    > [!important]  
    > Dashboard Apphas auth.ts which contains methods which I assume are used by AWS Cognito - it uses the authConfig defined in the orchestratorthe bulk of the components and business logic are defined here  
      
    > [!important]  
    > Sidebar Apphas auth.ts which contains methods which I assume are used by AWS Cognito - it uses the authConfig defined in the orchestratorhas components for navigationSidebarLinks.tsxthis is what happens when a link is clickedconst openLink = (linkItem: SidebarLinksConfig) => {  
    analyticsEvent({  
    category: AnalyticsCategory.SIDEBAR_LINKS,  
    action: "Link clicked",  
    label: linkItem.id === "openGlobal" ? "Open Global" : linkItem.url  
    });  
      
    if (linkItem.target) {  
    window.open(  
    `${linkItem.url}?token=${getAuthTokens().idToken}&target=${  
    linkItem.target  
    }`  
    );  
    return;  
    }  
    if (linkItem.external) {  
    if (linkItem.withAuth) {  
    window.open(`${linkItem.url}?${getAuthenticateRedirectURLParams()}`);  
      
    return;  
    }  
    window.open(linkItem.url);  
    return;  
    }  
      
    if (linkItem.id === "openGlobal") {  
    orchestrator.channel.publish("sidebar.openGlobalFilingSystem");  
    return;  
    }  
      
    // eslint-disable-next-line @typescript-eslint/no-unused-expressions  
    props.isMobile &&  
    setTimeout(() => {  
    props.setSidebarOpen(false);  
    }, 200);  
    props.history.push(linkItem.url);  
    };  
    
- Blog Post
    
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
    
    Module Federation is a code-transport layer - it is like a pipeline between applications which allows us to share code (Javascript) between applications at runtime. It does not inherently manage the lifecycles of applications as single-spa does, we need to do this ourselves. Module-federation is also a two-way street - code can be shared both ways between applications. For example, Application A can share code with Application B and vice versa. Module federation can be used to load application into a shell, and then these applications can be loaded via routing (react-router for react) in the shell application.
    
    Single-spa is a UI architecture - it defines how we should layout our shell application as well as the micro-front-end applications. It also manages the lifecycle of applications such as when and how they are bootstrapped, mounted (how they are added to the DOM), and unmounted (how they are removed from the DOM). Single-spa relies on SystemJS which is a **module loader** (code that runs in the browser to load our micro-frontend bundles).
    
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
    - `build` - settings that Vite uses during build time. This part is important and we will circle back.
        
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
    
    One of the nice things that Single-spa does for us is manage the lifecycles of MFE applications in the root-config (shell) via the Lifecycle methods (bootstrap, mount, and unmount) which define **how** applications are added to the DOM and activity functions which decide **when** the application are rendered to the user. Our POC will need to do the same. Let us deal with the **when** first.
    
    The way we will achieve this is by using React Router in our shell application. This will work by having different routes for each of the React, Vue and Angular applications. When the browser is on a route, we simply load that applications code into the browser and render it on the DOM. For example when we are on the `/react` route we will load the code from `App.jsx` into the Browser. Here is a diagram illustrating this. Also, note that our shell application will need to be a React app if we choose to the react router.
    
    ![[/Untitled 70.png|Untitled 70.png]]
    
      
    
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
    
      
    
- Setting up github CICD
    
    - can we have multiple CICD pipelines, one for each folder? Or just 1 in the root?
    - NX Affected command
    
      
    
    Different workflow file for different events
    
    - one for deploy
    - one for building and testing
    
      
    
    Look at using reuseable workflows for building and testing
    
      
    
    deploy to dev on pull_request open
    
    deploy to prod on merge to main
    
      
    
    **Steps:**
    
    React-app
    
    - install deps
    - testing
    - build
    - deploy
    - update import-map
    
    Angular-app
    
    - install deps
    - testing
    - build
    - deploy
    - update import-map
    
    Vue-App
    
    - install deps
    - testing
    - build
    - deploy
    - update import-map
    
    Shell
    
    - install deps
    - testing
    - build
    - deploy
    - update import-map
    
    Shared-UI
    
    - install deps
    - testing
    - build
    - publish
    
    Utility-module
    
    - install deps
    - testing
    - build
    - publish
- Update import map after deploy> [!important]  
    > after_deploy.shecho "Getting the environment: $ENV"  
    source .env.$ENV  
    echo "Downloading import map from S3"  
    aws s3 cp s3://$ORCHESTRATOR_BUCKET/$IMPORT_MAP_FILE $IMPORT_MAP_FILE  
    echo "Updating import map to point to new version of @nutrien-she/root-config"  
    ./node_modules/.bin/env-cmd -f .env.$ENV node update-importmap.mjs  
    echo "Uploading new import map to S3"  
    aws s3 cp import-map.json s3://$ORCHESTRATOR_BUCKET/$IMPORT_MAP_FILE --cache-control 'public, must-revalidate, no-store' --acl 'public-read' --expires 2014-01-01T00:00:00  
    echo "Invalidating cache on Cloudfront"  
    aws cloudfront create-invalidation --distribution-id $ORCHESTRATOR_CLOUDFRONT_ID --paths /$IMPORT_MAP_FILE  
    
      
    
    > [!important]  
    > update-importmap.mjs// Note that this file requires node@13.2.0 or higher (or the --experimental-modules flag)  
    import fs from 'fs';  
    import path from 'path';  
    import { v4 as uuidv4 } from 'uuid';  
      
    const importMapFilePath = path.resolve(process.cwd(), process.env.IMPORT_MAP_FILE);  
    const importMap = JSON.parse(fs.readFileSync(importMapFilePath));  
    const moduleName = process.env.MODULE_NAME;  
    const url = importMap.imports[moduleName];  
    const hash = uuidv4();  
      
    try {  
    const urls = url.split('?');  
    importMap.imports[moduleName] = `${urls[0]}?v=${hash}`;  
      
    fs.writeFileSync(importMapFilePath, JSON.stringify(importMap, null, 2));  
    // eslint-disable-next-line no-console  
    console.log(`Updated import map for module ${moduleName}.`);  
    } catch (error) {  
    throw Error(`Error adding the hash ${hash} into the url ${url}. Error was '${error.message}'`);  
    }  
      
    
      
    

  

dev shell: [https://dk0doaty0sorv.cloudfront.net/](https://dk0doaty0sorv.cloudfront.net/)

dev import map:

```JavaScript
{
  "imports": {
    "react": "https://esm.sh/react",
    "react-dom": "https://esm.sh/react-dom",
    "react-router-dom": "https://esm.sh/react-router-dom",
    "react-app": "https://d2s3mw0v1f3cif.cloudfront.net/react-app-dev.js",
    "vue-app": "https://dct0zl4ldh29k.cloudfront.net/vue-app-dev.js",
    "angular-app": "https://d23m92cai2rvzc.cloudfront.net/angular-app-dev.js",
    "shared-ui": "https://d1cnbfnbeov4sy.cloudfront.net/shared-ui.js",
    "util-module": "https://dkksu0fhny9yu.cloudfront.net/index.js"
  }
}
```

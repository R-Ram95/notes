# Concepts and How Vite Works

- vite uses esbuild for the dev server and rollup production
- during dev, Vite serves native ESM modules - using esbuild (not built and not bundled) - which lends to it’s fast server-start and fast reload

**Bundle Based Dev Server:**

![[/Untitled 71.png|Untitled 71.png]]

Webpack runs a bundle based dev server. Webpack will bundle the application before serving it in dev. You can see this because the entry file will be a bundled file. Every time changes are made to the source code, the bundler needs to rebuild the whole bundle, as the app gets bigger (more source code) this build process takes longer.

Heres an example using the Nutrien SHE app. Both the orchestrator and Auth apps are using Webpack5. The Orchestrator is running on port 9000 and the Auth app is running on port 9002. A search of material for the MaterialUI library in the entry file of the auth app `nutrien-she-auth-app.js` shows up in the file which proves that Webpack is bundling the source code and serving it in dev. NOTE: Webpack is bundling to the `systemjs` format (the top of the file show `System.register(...)`

![[/Untitled 1 48.png|Untitled 1 48.png]]

  

**Native ESM Based Dev Server:**

![[/Untitled 2 38.png|Untitled 2 38.png]]

Vite runs a Native ESM based. Vite serves source code over native ESM which modern browsers are able the understand. The source code is served without bundling, i.e. the entry file still includes`import` statements. Vite leverages the fact that modern browsers support native ESM and lets the browser resolve the imports rather than using the build process to build and bundle before serving the files in dev. Vite only needs to transform and serve source code as the browser requests it.

HMR in Vite is performed over native ESM. When a file is edited, Vite figures out and replaces the parts of the app are affected by the change up to the HMR boundary (point in app where HMR updates can be applied).

Here is an example using the xerris orchestrator app and the xerris auth app which are run on ports 9000 and 9002 respectively. The entry file for the xerris-auth-app contains the `import` statements fo react, react-dom, and single-spa. Note that Vite did not bundle the libraries into the entry file, rather, it lets the browser take care of this during run time.

![[/Untitled 3 36.png|Untitled 3 36.png]]

# Storybook

The builder does not read the `vite.config.ts/js` by default

[https://github.com/storybookjs/builder-vite](https://github.com/storybookjs/builder-vite)

# Typescript

- Supports importing `.ts` files out of the box
- only performs transpilation and NOT type checking
    - see `noEmit` ts options
- uses `esbuild` to transpile ts to js

Ts Config Options

- `isoltedModules: true`
    - `esbuild` performs transpilation without type info ⇒ this lets TS warn you against features that don’t work with isolated transpilation
- `useDefineForClassFields: true` ⇒ for most libs, check others

# Files

Typescript does type checking, vite does transpilation, vite builds the library and the storybook. testing is done using vitest

`tsconfig.json`

- used when `tsc` is used
- configuration for the Typescript Compiler
- performs type checking and transpilation
- uses the `noEmit: true` option which means that no output files are generated

`tsconfig.vite.json`

- used when `vite build` command is used
- configuration for the Vite builder
- only transpilation from typescript to js
- has reference to `vite.config.js`

`vite.config.js`

- configuration options for building the library
    - note rollup is used to build the library because rollup is production build optimized
    - vite is for fast dev servers
- configuration options for testing
    - uses the JSDOM environment for testing
    - specifies the `setupTests.ts` needs to be run before starting the tests
- `lib`
    - specifies entry point for library build (`src/index.ts`)
    - output formats are ES module and CommonJS
- `rollUp`
    - configure the rollup bundle which vite uses to generate the final build
- by default, output directory is `./dist`

`storybook/main.cjs`

- the `core.builder` option specifies that when `npm run build-storybook` command is executed, the Stroybook Builder for Vite is used which is a special version of Vite that is optimized for building Storybook projects
- uses `viteFinal` hook to customize the Vite configuration
- also specifies `addons` which are used to enhance functionality in Storybook, for example, controlling props, settings bac4kgrounds, etc.

`src/setupTests.ts`

- imports `matchers` from `jest-dom` that provides functions (`toBeVisible, toHaveStyle, etc.`) that help in writing tests
- extends the functionality of Vitest’s `expect` function to use the matchers above
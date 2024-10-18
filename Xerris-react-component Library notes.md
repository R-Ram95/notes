---
tags:
  - Frontend
  - Project-Notes
---
# Files

typescript does type checking, vite does transpilation, vite builds the library and the storybook. testing is done using vitest

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
- also specifies `addons` which are used to enhance functionality in Storybook, for example, controlling props, settings backgrounds, etc.

`src/setupTests.ts`

- imports `matchers` from `jest-dom` that provides functions (`toBeVisible, toHaveStyle, etc.`) that help in writing tests
- extends the functionality of Vitest’s `expect` function to use the matchers above
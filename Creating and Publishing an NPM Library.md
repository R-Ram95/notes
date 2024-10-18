---
tags:
  - Frontend
  - Guide
  - npm
---
# Setting up Project to publish as Library

This procedure outlines how to setup a project to publish as a library. It outlines the file structure, config files for ts, etc. for publishing.

## File Structure

Use this file structure to allow user of library to import Button without knowing the full path (`src/components/Button/index.ts`)

```JSON
├── src
│   ├── components
|   │   ├── Button
|   |   │   ├── Button.tsx
|   |   │   └── index.ts
|   │   └── index.ts
│   └── index.ts
```

  

`src/components/Button/index.ts`

```JSON
export { default } from "./Button";
```

`src/components/index.ts`

```JSON
export { default as Button } from "./Button";
```

`src/index.ts`

```JSON
export * from './components'
```

  

## Adding Typescript

The following outlines how to add and configure typescript to the project.

Our compiler needs to know how to take `.tsx` file and convert it to a `.js` file

`tsconfig.json` ⇒ using `npx tsc --init`

```JSON
{
  "compilerOptions": {
    // Default
    "target": "es5", 
    "esModuleInterop": true, 
    "forceConsistentCasingInFileNames": true,
    "strict": true, 
    "skipLibCheck": true,

    // Added
    "jsx": "react",
    "module": "ESNext",  
    "declaration": true,
    "declarationDir": "types",
    "sourceMap": true,
    "outDir": "dist",
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true,
    "emitDeclarationOnly": true,
  }
}
```

- `"jsx": "react"` — Transform JSX into react
- `"module": "ESNext"` — generates ES6 JS modules for library
- `"declaration": true` — Output `.d.ts` file for our library type
- `“declarationDir": "types"` — Where `.d.ts` files are placed
- `"sourceMap": true` — Maps JS code back to TS file origins for debugging
- `"outDir": "dist"` — Directory where the project will be generated
- `"moduleResolution": "node"` — Follow `node.js` rules for finding modules
- `"allowSyntheticDefaultImports": true` — Assume default exports if none are created manually
- `"emitDeclarationOnly": true` — Don’t generate JS ⇒ bundler will do that ⇒ only export type declarations

## Adding Rollup

The following outlines how to add Rollup to the project but note that similar steps can be followed for other bundlers

Required plugins:

- `@rollup/plugin-node-resolve` - Uses the [node resolution algorithm](https://nodejs.org/api/modules.html#modules_all_together) for modules ⇒ tells rollup to how resolve our modules
- `@rollup/plugin-typescript` - teaches rollup how to process typescript files
- `@rollup/plugin-commonjs` - converts commonjs modules to ES6 modules ⇒ lets use bundle libraries together using ES6 AND commonjs modules
- `rollup-plugin-dts` - roll up `.d.ts` files

`rollup.config.js` - Configuration file to tell rollup how to bundle library

```JSON
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import typescript from "@rollup/plugin-typescript";
import dts from "rollup-plugin-dts";

const packageJson = require("./package.json");

export default [
// this object is for js files (our components)
  {
    input: "src/index.ts",
    output: [
      {
        file: packageJson.main,
        format: "cjs",
        sourcemap: true,
      },
      {
        file: packageJson.module,
        format: "esm",
        sourcemap: true,
      },
    ],
    plugins: [
      resolve(),
      commonjs(),
      typescript({ tsconfig: "./tsconfig.json" }),
    ],
  },
// this object is for the types
  {
    input: "dist/esm/types/index.d.ts",
    output: [{ file: "dist/index.d.ts", format: "esm" }],
    plugins: [dts()],
  },
];
```

  

# Publishing NPM library - Github Registry

This procedure is to publish an npm library from an existing github repo.

1) Modify `package.json`

- name field: `name: @GITHUB_USER_NAME/REPOSITORY_NAME`
- publish Config field: `publishConfig: { registry: https://npm.pkg.github.com/REPOSITORYNAME}`
- example

```JSON
"name": "@R-Ram95/template-react-component-library",
  "publishConfig": {
    "registry": "https://npm.pkg.github.com/template-react-component-library"
  },
```

2) require `.npmrc` in the root `~` directory

- registry field: `https://registry.npmjs/org/`
    - registry name: `@USER_NAME:registry=https://npm.pkg.github.com/`
- token ⇒ github access token created from github dashboard
    
    `//npm.pkg.github.com/:_authToken=GITHUB_TOKEN`
    

2.1) Github Auth Token Creation

1) go to [https://github.com/settings/tokens](https://github.com/settings/tokens)

2) Generate new token

3) Select `write:packages` permission

![[/Untitled 69.png|Untitled 69.png]]

## Using Private Repo

1) make `.npmrc` in root `~` directory

2) copy the same registry fields of step 2

3) select `read:packages` permission

![[/Untitled 1 47.png|Untitled 1 47.png]]

# Resources

> [!info] How to Create and Publish a React Component Library  
> All code from this tutorial as a complete package is available in this repository.  
> [https://dev.to/alexeagleson/how-to-create-and-publish-a-react-component-library-2oe](https://dev.to/alexeagleson/how-to-create-and-publish-a-react-component-library-2oe)
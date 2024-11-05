# how-to-setup-express-with-typescript-dev-hot-reload

1. Initilize the project

```Bash
    mkdir express-ts-server
    cd express-ts-server
    npm init -y

```

2. Install express, typescript, types, and ts-node-dev (used for hot-reloading
   typescript - https://www.npmjs.com/package/ts-node-dev)

```Bash
   npm install express
   npm install -D typescript @types/node @types/express ts-node-dev
```

3. Create `tsconfig.json`

```Bash
    npx tsc --init

```

```JSON
    {
        "compilerOptions": {
            "target": "es6",
            "module": "ESNext",
            "rootDir": "./src",
            "outDir": "./dist",
            "strict": true,
            "esModuleInterop": true
        }
    }
```

4. Add this to `package.json` to run in Dev mode

```JSON
    "scripts": {
          "dev": "ts-node-dev --respawn --transpile-only src/server.ts"
    }
```

-  `--respawn` restarts the server on changes
-  `--transpile-only` skips type checking for faster reloads
  - you can run tsc separately for type checking

5. (optional) To use ES6 Imports/Exports

Change `tsconfig.json`

```JSON
    {
        "compilerOptions": {
            "target": "es6",
            "module": "CommonJS", 
            "moduleResolution": "node", // Makes TS follow Node.js module resolution
            "rootDir": "./src",
            "outDir": "./dist",
            "strict": true,
            "esModuleInterop": true,
           "allowSyntheticDefaultImports": true, /* Allow 'import x from y' when a module doesn't have a default export. */
        }
    }
```

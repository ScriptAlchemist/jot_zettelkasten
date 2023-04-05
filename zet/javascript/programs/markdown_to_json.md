# Creating a Markdown to JSON processing program.

First we need to make sure that we have `Node.js` Installed. This makes
sure that we have `npm` available.

## Project Setup

First we will make the project folder and enter the folder.

1. `mkdir markdown-to-json`
2. `cd markdown-to-json`

After this we need to setup `npm`. This would be considered
**Initializing a new Node.js Project**

3. `npm init` or `npm init -y`. The `-y` flag allows for automatically
   choosing Yes when prompted. Make sure you know what you are approving
   before you do it blindly.

We should add the basic packages we need to accomplish the task at hand.

4. `npm install typescript --save-dev`
5. `npm install @types/node fs path markdown-it`

We now have a few different packages downloaded. Let's talk about them
real quick.

* `typescript`: this is the typescript package that will be used to the
  `dev` section. As you can tell with `--save-dev`.
* `@types/node`: the type file `d.ts` for `Node`.
* `fs`: Node file system package
* `path`: Node path package
* `markdown-it`: markdown processing package.

Let's also add a few more packages while we are here.

6. `npm install tsdoc typedoc --save-dev`
7. `npm install minimist`

What do these do?

* `tsdoc`: I'm not 100% sure if we need this. But we have it.
* `typedoc`: How we will generate our documentation. I'm just not sure
  if tsdoc is a dependency or not. We can delete it later.
* `minimist`: This is to take in tags and process them easily
  - `-i`: input file
  - `-o`: output file (optional)

Now those are the basic packages we would like to start with. Now let's
setup some of the file.

8. `npx tsc --init` This will generate the basic typescript config for
   us.
9. `mkdir src` We will create our `src` directory to build inside.
10. Update the tsconfig file with the following:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules"]
}
```
Let's break it down:

* `compilerOptions`: The compilation options we want to choose
* `target: "ES2020"`: The target the our JavaScript will compile to
* `module: commonjs`: The module style for the compilation
* `outDir: ./dist`: Where the compiled files will output into
* `strict: true`: We want strict node rules
* `esModuleInterop: true`: We allow es module Interoperability
* `include: ["src/**/*.ts"]`: We will include the `src/` file and any
  `**/*.ts` typescript files one layer deep inside of the `src`
  directory.
* `exclude: ["node_modules"]`: Exclude the packages in the compilation

11. Now we need to update the `package.json` file with a few scripts.
    Like so:

```json
"script": {
  "build": "tsc",
  "start": "node dist/index.js",
  "docs": "typedoc --out docs src --gitRemote origin"
}
```




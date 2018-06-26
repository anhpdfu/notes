# Debugging ES6 in Visual Studio Code

I come from a Java background. Debugging a Java app is easy. You just configure your IDE for the latest version of the JDK, write your app using all of the latest syntactic goodness, add a breakpoint, and run it in debug mode. Voila! You’re debugging…

These days I write mostly JavaScript (Node.js and browser). I’m constantly amazed how complex and cumbersome it is to debug a simple ES6 JavaScript app. Why can’t we just tell our editor/IDE the version of JavaScript we’re targeting and start debugging? If only it was that easy…

As a long time satisfied JetBrains customer, I recently switched from WebStorm to Visual Studio Code. I regularly like to give the open source editors a try but I’ve always gone back to IntelliJ or WebStorm. Eclipse? NetBeans? Atom? Brackets? Tried them all but always returned “home” in frustration. However, VS Code is my latest fling and it’s looking like this relationship may have a chance.

Now that I’ve decided on an editor, I’d really like to be able to quickly build an ES6 app and debug it to work out the kinks. Fortunately, after a couple hours reading documentation and experimenting, I’ve come across a fairly simple approach to running and debugging a server-side ES6 application in VS Code.

## Step 1 — Install Node.js 8.x
The easiest way I’ve found to debug ES6 in VS Code is to use the latest Node.js release. This version uses the new “inspector” debug protocol that is much more robust than the legacy protocol used in older versions. If you haven’t installed Node yet, install it from here:

https://nodejs.org/

If you already have it installed, check the version like this:

```bash
$ node --version
```

If you’re not running version 8.x (or later), you can install the latest version using a Node version manager like “n” here:

https://github.com/tj/n

Or “nvm” here:

https://github.com/creationix/nvm

Personally, I prefer “n” but either solution works well for installing and managing multiple versions of Node. Once you’ve used one of these tools to install the latest version of Node and verified from the command-line that you’ve switched to this version, you’re ready to move on.

## Step 2 — Configure a New ES6 Project

Let’s kick things off by creating a very simple npm project and launching it in VS Code:

```bash
$ mkdir debug-es6 && cd debug-es6
$ npm init -f
$ code .
```

Of course, this assumes that you’ve already installed Visual Studio Code. If not, do that now. Go ahead. I’ll wait.

Next we’ll need to install the necessary Babel modules in order to transpile our code. This command will do the trick:

```bash
$ npm install --save-dev babel-cli babel-preset-es2015
```

`babel-cli` is the Babel compiler and `babel-preset-es2015` is a plugin that adds ES6 support. Once those are installed, we’ll need to update the `package.json` file or create a `.babelrc` file in order to configure Babel to use the ES6 plugin. For simplicity, we’ll just update `package.json` like this:

```javascript
  "devDependencies": {
    "babel-cli": "^6.24.1",
    "babel-preset-es2015": "^6.24.1"
  },
  "babel": {
    "presets": [
      "es2015"
    ]
  }
```
  
Now let’s write a simple app that we can debug. Go ahead and create a `src` folder in your project root and a `src/math.js` file that looks like this:

```javascript
export function add(num1, num2) {
  return num1 + num2;
}
export function multiply(num1, num2) {
  return num1 * num2;
}
```

Also create a `src/app.js` file that exercises our math module like this:

```javascript
import {add, multiply} from './math';
const num1 = 5, num2 = 10;
console.log('Add: ', add(num1, num2));
console.log('Multiply: ', multiply(num1, num2));
```

That should be enough to establish a rudimentary debug session. Let’s move along…

## Step 3 — Configure Babel to Transpile ES6 to ES5

The first step to debugging ES6 is configuring a transpiler to translate your ES6 code to something that Node can run natively. In other words, ES5. But in addition to the transpiled code, we also need to generate source maps that will allow the Node debugger in VS Code to map breakpoints between our source and compiled code.

Of course, there are ways to debug without configuring a transpiler. However, I never had much luck with solutions like `babel-node` and `babel-register`. Either the debug environment seemed brittle or custom code was required to bootstrap the debugging process. I was looking for a simple solution that would work in practically all scenarios. Configuring Babel to transpile the code and generate source maps seemed to be the ticket…

In addition to transpiling our code and generating source maps, we’d like Babel to keep watching our code for changes and re-compile as necessary. This keeps our write-compile-debug loop nice and tight. To do this, add a “compile” script to your `package.json` file as shown here:

```javascript
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "compile": "babel src --out-dir .compiled --source-maps --watch"
},
```

This script will transpile the code in the `src` folder and output the results to the `.compiled` folder. The `--source-maps` and `--watch` options instruct Babel to generate source maps and continue watching the source files for changes (and re-compile whenever changes occur). Start the Babel compiler:

```bash
$ npm run compile
```

We now have a basic ES6 application that’s automatically compiled whenever changes are made. Excellent!

## Step 4 — Add Launch Configuration

It’s time to add a VS Code Launch Configuration in order to run your application in debug mode. In the menu bar, click “Debug -> Open Configurations” and choose “Node.js” when prompted to select an environment. Update the resulting file to look like this:

```javascript
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch App.js",
      "program": "${workspaceRoot}\\src\\app.js",
      "outFiles": [ "${workspaceRoot}\\.compiled\\**\\*.js" ]
    }
}
```

The `type` and `request` options in this configuration indicate that you’ll be using Node.js to run your ES6 application and VS Code should launch it for you (as opposed to attaching to an already running Node process). `program` specifies the entry point to your application and `outFiles` tells VS Code where to find the compiled files (and source maps) which will actually be used during execution of your program. The source maps will allow the editor to map compiled ES5 code back to your ES6 source.

## Step 5 — Debug your Application

After creating a launch configuration, let’s go ahead and set a couple breakpoints. You can set breakpoints by clicking in the gutter just to left of the line number. Add a breakpoint to the last line of `src/app.js` and another inside one of the functions in `src/math.js`. Go back to the Debug tab by clicking the debug icon in the left-hand navigation bar, selecting the “Launch App.js” launch configuration from the drop-down list at the top of the debug window, and clicking the green “Start Debugging” arrow. You should hit your first breakpoint!

Note that our launch configuration is set up to always run the `src/app.js` application. What if you’d like to debug the file currently selected and visible in the editor? You can do that by adding a second launch configuration. Select “Debug -> Open Configurations” from the menu bar, copy/paste the existing launch configuration, and modify the copy to look like this:

```javascript
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch App.js",
      "program": "${workspaceRoot}\\src\\app.js",
      "outFiles": [ "${workspaceRoot}\\.compiled\\**\\*.js" ]
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Current File",
      "program": "${file}",
      "outFiles": [ "${workspaceRoot}\\.compiled\\**\\*.js" ]
    }
}
```

You can now debug the file that’s currently visible in the editor by clicking the debug icon, choosing the “Launch Current File” configuration from the drop-down list, and clicking the green run button.

## Conclusion

Whew! Looking back at these steps, it appears to be a lot more work than it actually is. Once you get used to adding the Babel dependencies and creating a launch configuration, setting up an ES6 project for debugging is actually pretty quick. The transpilation process certainly adds some overhead but with just a little effort, you’ll be debugging your ES6 projects in no time.

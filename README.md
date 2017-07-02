# generate-package-json-webpack-plugin

> For limiting the dependencies inside `package.json` to only those that are actually being used by your code.

### Why?

This plugin is useful for when you have a large source project for development from which smaller Node.js projects are bundled for various deployments and applications. Such as Google Cloud Functions.

### Install :floppy_disk:

```
npm install generate-package-json-webpack-plugin --save-dev
```

## Usage :electric_plug:

```
const basePackageValues = {
  "name": "my-nodejs-module",
  "version": "1.0.0",
  "main": "./index.js",
  "engines": {
    "node": "<= 6.9.1"
  }
}

// inside your webpack configuration
plugins: [new GeneratePackageJsonPlugin(basePackageValues)],
```

That's pretty much it. The plugin will generate a new `package.json` file with all the dependencies your bundle uses, merged with the values you pass into the plugin.

### Simple API

```
new GeneratePackageJsonPlugin(basePackageValues, versionsPackageFilename)
```

### First argument: `otherPackageValues`

( **Recommended** ) You should set the base values for your `package.json` file here. For example:

```
const basePackageValues = {
  "name": "my-nodejs-module",
  "version": "1.0.0",
  "main": "./bundle.js",
  "engines": {
    "node": "<= 6.9.1"
  }
}
```

This will be merged with the dependencies to form the final `package.json` distribution file.

### Second argument: `versionsPackageFilename`

( **Optional** ) **unless** your `webpack.config.js` file is not in the same folder as your base projects `package.json` file.

This is the filename from which the plugin will source the versions for the modules that appear in this bundle. Usually it will just be the base `package.json` file in your parent directory (assuming that your development code has been using dependencies sourced from your base `node_modules` folder):

The **default** value inside the plugin is set like this:
```
const versionsPackageFilename = __dirname + "/package.json";
```

### Things to take note of :mag:

You should remember to set the `"main": "./index.js"` to the correct filename (would probably
be the output bundle file from the same webpack task), and / or correctly set your starting script
which will be run on Node.js server deployments by `npm start`. You can set these values in
the `basePackageValues` object you pass into the plugin, example:

```
const basePackageValues = {
  "name": "my-nodejs-module",
  "version": "1.0.0",
  "main": "./bundle.js",
  "scripts": {
    "start": "node ./bundle.js"
  },
  "engines": {
    "node": "<= 6.9.1"
  }
}
```

This plugin is best used in conjunction with something like [webpack-node-externals](https://github.com/liady/webpack-node-externals), which you can use to make sure your node modules are not included with your final `bundle.js`, like so:

```
const nodeExternals = require("webpack-node-externals");

// inside your webpack config
externals: [nodeExternals({
    whitelist: [/^module-I-want-bundled/],
})],
```
A webpack plugin that wraps output files (chunks) with custom text or code.
============

Installation
------------

Install locally using npm:  
`npm install --save-dev wrapper-webpack-plugin`

Usage
------------

`var WrapperPlugin = require('wrapper-webpack-plugin');`

The `WrapperPlugin` class has a single parameter, an object with a `header` and/or `footer` properties. Header text will
be *prepended* to the output file, footer text will be *appended*. These can be either a string or a function. A string
will simply be a appended/prepended to the file output. A function is expected to return a string, and will receive the
name of the output file as an argument.

Example `webpack.config` #1
------------

Wraps bundles in a self invoking function and enables strict mode:

```javascript
var WrapperPlugin = require('wrapper-webpack-plugin');

module.exports = {
  // other webpack config here
  
  plugins: [
    // strict mode for the whole bundle
    new WrapperPlugin({
      header: '(function () { "use strict";\n',
      footer: '\n})();'
    })
  ]
};
```

Example `webpack.config` #2
------------

Prepends bundle with a doc comment:

```javascript
var WrapperPlugin = require('wrapper-webpack-plugin');

module.exports = {
  // other webpack config here
  
  plugins: [
    new WrapperPlugin({
      header: function (fileName) {
        return '/*! file: ' + fileName + ', created by dev123 */\n';
      }
    })
  ]
};
```

Example `webpack.config` #3
------------

Keeping header in a separate file:

file: `header.js`
```javascript
/*!
 * my awesome app!
 */
```

file: `webpack.config`
```javascript
var fs = require('fs');
var WrapperPlugin = require('wrapper-webpack-plugin');

var headerDoc = fs.readFileSync('./header.js', 'utf8');

module.exports = {
  // other webpack config here

  plugins: [
    new WrapperPlugin({
      header: headerDoc
    })
  ]
};
```

Example `webpack.config` #4
------------

A slightly more complex example using `lodash` templates:

```javascript
var WrapperPlugin = require('wrapper-webpack-plugin');
var template = require('lodash.template');
var pkg = require('./package.json');

var tpl = '/*! <%= appName %> v<%= version %> | <%= author %> */\n';

var tplParams = {
  appName: 'myapp',
  author: 'dev123',
  version: pkg.version
};

module.exports = {
  // other webpack config here

  plugins: [
    new WrapperPlugin({
      header: function () {
        return template(tpl)(tplParams);
      }
    })
  ]
};
```

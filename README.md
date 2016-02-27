# add-asset-html-webpack-plugin
> Add a JavaScript or CSS asset to the HTML generated by `html-webpack-plugin`

[![NPM Version][npm-image]][npm-url]

[![Dependency Status][david-image]][david-url]
[![Dev Dependency Status][david-dev-image]][david-dev-url]
[![Peer Dependency Status][david-peer-image]][david-peer-url]

## Installation
Install the plugin with `npm`:
```sh
$ npm i add-asset-html-webpack-plugin -D
```

## Basic Usage
The plugin will add the given JS or CSS file to the files Webpack knows about, and put it into the list of assets
`html-webpack-plugin` injects into the generated html. Add the plugin the your config, providing it a filename:

```js
var HtmlWebpackPlugin = require('html-webpack-plugin')
var AddAssetHtmlPlugin = require('add-asset-html-webpack-plugin')
var webpackConfig = {
  entry: 'index.js',
  output: {
    path: 'dist',
    filename: 'index_bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new AddAssetHtmlPlugin({ filename: require.resolve('./some-file') })
  ]
}
```

This will add a script tag to the HTML generated by `html-webpack-plugin`, and look like:
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script>
    <script src="some-file.js"></script>
  </body>
</html>
```

## Options
Options are passed to the plugin during instantiation.

```js
new AddAssetHtmlPlugin({ filename: require.resolve('./some-file') })
```

#### `filename`
Type: `string`, mandatory

The absolute path of the file you want to add to the compilation, and resulting HTML file.

#### `includeSourcemap`
Type: `boolean`, default: `false`

If `true`, will add `filename + '.map'` to the compilation as well.

#### `typeOfAsset`
Type: `string`, default: `js`

Can be set to `css` to create a `link`-tag instead of a `script`-tag.

## Examples
### Add a DLL file from webpack.DllPlugin
Note: Remember to build the DLL file in a separate build.

When adding assets, it's added to the start of the array, so when
`html-webpack-plugin` injects the assets, it's before other assets. If you
depend on some order for the assets beyond that, you'll have to create some
sort of intermediate file in the given order.

#### Webpack config
```js
var path = require('path')
var webpack = require('webpack')
var webpackConfig = {
  entry: {
    vendor: ['react', 'redux', 'react-router']
  },
  devtool: '#source-map',
  output: {
    path: path.join(__dirname, 'build'),
    filename: '[name].dll.js',
    library: '[name]_[hash]'
  },
  plugins: [
    new webpack.DllPlugin({
      path: path.join(__dirname, 'build', '[name]-manifest.json'),
      name: '[name]_[hash]'
    }),
  ],
}
```

Your main build:
```js
var path = require('path')
var webpack = require('webpack')
var HtmlWebpackPlugin = require('html-webpack-plugin')
var AddAssetHtmlPlugin = require('add-asset-html-webpack-plugin')
var webpackConfig = {
  entry: 'index.js',
  output: {
    path: 'dist',
    filename: 'index_bundle.js'
  },
  plugins: [
    new webpack.DllReferencePlugin({
      context: path.join(__dirname),
      manifest: require('./build/vendor-manifest.json')
    }),
    new HtmlWebpackPlugin(),
    new AddAssetHtmlPlugin({
      filename: require.resolve('./build/vendor.dll.js'),
      includeSourcemap: true
    })
  ]
}
```


[npm-url]: https://npmjs.org/package/add-asset-html-webpack-plugin
[npm-image]: https://img.shields.io/npm/v/add-asset-html-webpack-plugin.svg
[david-url]: https://david-dm.org/SimenB/add-asset-html-webpack-plugin
[david-image]: https://img.shields.io/david/SimenB/add-asset-html-webpack-plugin.svg
[david-dev-url]: https://david-dm.org/SimenB/add-asset-html-webpack-plugin#info=devDependencies
[david-dev-image]: https://img.shields.io/david/dev/SimenB/add-asset-html-webpack-plugin.svg
[david-peer-url]: https://david-dm.org/SimenB/add-asset-html-webpack-plugin#info=peerDependencies
[david-peer-image]: https://img.shields.io/david/peer/SimenB/add-asset-html-webpack-plugin.svg
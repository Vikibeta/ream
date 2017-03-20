# unvue

[![NPM version](https://img.shields.io/npm/v/unvue.svg?style=flat)](https://npmjs.com/package/unvue) [![NPM downloads](https://img.shields.io/npm/dm/unvue.svg?style=flat)](https://npmjs.com/package/unvue) [![Build Status](https://img.shields.io/circleci/project/egoist/unvue/master.svg?style=flat)](https://circleci.com/gh/egoist/unvue) [![codecov](https://codecov.io/gh/egoist/unvue/branch/master/graph/badge.svg)](https://codecov.io/gh/egoist/unvue)
 [![donate](https://img.shields.io/badge/$-donate-ff69b4.svg?maxAge=2592000&style=flat)](https://github.com/egoist/donate)

> unopinionated, universal Vue.js app made simple

## Why

This isn't just another Next.js clone, bascially this is based on [vue-hackernews-2.0](https://github.com/vuejs/vue-hackernews-2.0) and extracted as a reusable module.

Comparing to Next.js and Nuxt.js, with unvue you can use any file structure you like, the only requirement is to export vue-router instance in `src/index.js`

[Check out all the features.](/docs/features.md)

## Install

```bash
yarn add unvue express
```

## Usage

```js
// server.js
const app = require('express')()
const unvue = require('unvue')

unvue(app, {
  dev: process.env.NODE_ENV !== 'production',
  postCompile() {
    console.log('> Open http://localhost:3000')
  }
})

app.listen(3000)
```

Run `node server.js`.

This assumes you have `src/index.js` in current working directory and it exports at least `app` and `router`:

```js
// your vue router instance
import router from './router'

export { router }
```

### preFetch

Every router-view component can have a `preFetch` property to pre-fetch data to fill Vuex store on the server side, this requires you to export `store` in `src/index.js` too.

```js
export default {
  preFetch({ store }) {
    return store.dispatch('asyncFetchData')
  }
}
```

If the action you want to perfom in `preFetch` method is async, it should return a Promise.

### Modify `<head>`

`unvue` uses [vue-meta](https://github.com/declandewet/vue-meta) under the hood, so you can just set `head` property on Vue component to provide custom head tags:

```js
export default {
  head: {
    title: 'HomePage'
  }
}
```

Check out [vue-meta](https://github.com/declandewet/vue-meta) for details, its usage is the same here except that we're using `head` instead of `metaInfo` as key name.

### createConfig

Create webpack config only.

```js
const createConfig = require('unvue/lib/create-config')

const config = createConfig({
  type, // `server` or `client`
  dev,
  // ...
})

const webpackConfig = config.toConfig()
// perform your own build process
```

The `config` is a [webpack-chain](https://github.com/mozilla-neutrino/webpack-chain) config instance, which allows you to easily modify original webpack config.

## API

### unvue(app, [options])

#### app

Express app instance.

#### options

##### html

Options for `html-webpack-plugin`, by default it is:

```js
{
  title: 'UNVUE',
  template: 'built-in template'
}
```

##### dev

Type: `boolean`<br>
Default: `false`

Run server in development mode which has hot-reloading enabled.

##### cwd

Type: `string`<br>
Default: `process.cwd()`

##### postCompile

Type: `function`

Invoke a function when compilation is done. *`unvue` only*

##### extendWebpack

Type: `function`

Extend webpack config generated by [webpack-chain](https://github.com/mozilla-rpweb/webpack-chain), for example to show progress bar while building client bundle in production mode:

```js
const ProgressPlugin = require('webpack/lib/ProgressPlugin')

unvue({
  extendWebpack(config, { type, dev }) {
    if (type === 'client' && !dev) {
      config.plugin('display-progress')
        .use(ProgressPlugin)
    }
  }
})
```

### createConfig([options])

#### options

Same as options in `unvue` except `unvue only` options.

## FAQ

### Here's a missing feature!

**"Can you update webpack config *this way* so I can use that feature?"** If you have the same question, before we actually think this feature is necessary and add it, you can [extend webpack config](#extendwebpack) yourself to implement it. With [webpack-chain](https://github.com/mozilla-rpweb/webpack-chain) you have full control of our webpack config, check out the default [config instance](/lib/create-config.js).

## Contributing

1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request :D


## Author

**unvue** © [egoist](https://github.com/egoist), Released under the [MIT](./LICENSE) License.<br>
Authored and maintained by egoist with help from contributors ([list](https://github.com/egoist/unvue/contributors)).

> [egoistian.com](https://egoistian.com) · GitHub [@egoist](https://github.com/egoist) · Twitter [@rem_rin_rin](https://twitter.com/rem_rin_rin)

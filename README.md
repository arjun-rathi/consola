<p align="center">
  <h1>🐨 Consola</h1>
  <span>Elegant Console Logger for Node.js and Browser</span>
</p>

[![Standard JS][standard-js-src]][standard-js-href]
[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![package phobia][package-phobia-src]][package-phobia-href]
[![bundle phobia][bundle-phobia-src]][bundle-phobia-href]

## Why Consola?

- Easy to use
- Fancy output with fallback for minimal environments
- Pluggable reporters
- Consistent command line interface (CLI) experience
- Tag support
- Redirect `console` and `stdout/stderr` to the consola and easily restore redirect.
- Browser support
- Pause/Resume support
- Mocking support

## Installation

Using yarn:

```bash
yarn add consola
```

Using npm:

```bash
npm i consola
```

## Getting Started

```js
const consola = require('consola')

// See types section for all available types

consola.success('Built!')
consola.info('Reporter: Some info')
consola.error(new Error('Foo'))
```

## Methods

#### `<type>(logObject)` `<type>(args...)`

Log to all reporters.

#### `addReporter(reporter)`

Register a custom reporter instance.

#### `removeReporter(reporter?)`

Remove a registered reporter.

If no arguments are passed all reporters will be removed.

#### `setReporters(reporter|reporter[])`

- Type: `Object` or `Array`

Replace all reporters.

#### `create(options)`

Create a new `Consola` instance and inherit all parent options for defaults.

#### `withDefaults(defaults)`

Create a new `Consola` instance with provided defaults

#### `withTag(tag)`

Create a new `Consola` instance with that tag.

#### `wrapConsole()` `restoreConsole()`

Globally redirect all `console.log`, etc calls to consola handlers.

#### `wrapStd()` `restoreStd()`

Globally redirect all stdout/stderr outputs to consola.

#### `wrapAll()` `restoreAll()`

Wraps both std and console.

console uses std in the underlying so calling `wrapStd` redirects console too.
Benefit of this function is that things like `console.info` will be correctly redirected to the corresponding type.

#### `pause()` `resume()`

**Globally** pause and resume logs.

Consola will enqueue all logs when paused and then sends them to the reported when resumed.

#### `mockTypes`

Mock all types. Useful for using with tests.

The first argument passed to `mockTypes` should be a callback function accepting `(typeName, type)` and returning the mocked value:

```js
consola.mockTypes((typeName, type) => jest.fn())
```

Please note that with the example above, everything is mocked independently for each type. If you need one mocked fn create it outside:

```js
const fn = jest.fn()
consola.mockTypes(() => fn)
```

If callback function returns a _falsy_ value, that type won't be mocked.

For example if you just need to mock `consola.fatal`:

```js
consola.mockTypes((typeName) => typeName === 'fatal' && jest.fn())
```

**NOTE:** Any instance of consola that inherits the mocked instance, will apply provided callback again.
This way, mocking works for `withTag` scopped logers without need to extra efforts.

## Fields

#### `reporters`

An array of active reporters.

#### `level`

The level to display logs. Any logs at or above this level will be displayed.
List of available levels [here](./src/types.js).

You can set log level using `CONSOLA_LEVEL` environment variable.

## logObject

logObject is a free-to-extend object which will be passed to reporters.

Standard fields:

- `message`
- `additional`
- `args`
- `date`
- `tag`

## Reporters

Choose between one of the built-in reporters or bring own reporter.

By default `FancyReporter` is registered for modern terminals or `BasicReporter` will be used if running in limited environments such as CIs.

Available reporters:

- [BasicReporter](./src/reporters/basic.js)
- [FancyReporter](./src/reporters/fancy.js)
- [JSONReporter](./src/reporters/json.js)
- [WinstonReporter](./src/reporters/winston.js)

### Creating your own reporter

A reporter (Class or Object) exposes `log(logObj)` method.
To write a reporter, check implementations to get an idea.

## Types

Types are _logging levels_. A list of all available default types is [here](./src/types.js).

## Creating a new instance

Consola has a global instance and is recommended to use everywhere.
In case more control is needed, create a new instance.

```js
import consola from 'consola'

const logger = consola.create({
    // level: 4,
    reporters: [
      new consola.JSONReporter()
    ],
    defaults: {
      additionalColor: 'white'
    }
})
```

## Integrations

### With jest

```js
consola.setReporters({
  log: jest.fn()
})
```

### With jsdom

```js
{
  virtualConsole: new jsdom.VirtualConsole().sendTo(consola)
}
```

## License

MIT - Made with 💖 By Nuxt.js team!

<!-- Refs -->
[standard-js-src]: https://flat.badgen.net/badge/code%20style/standard/green
[standard-js-href]: https://standardjs.com

[npm-version-src]: https://flat.badgen.net/npm/v/consola/latest
[npm-version-href]: https://npmjs.com/package/consola

[npm-downloads-src]: https://flat.badgen.net/npm/dt/consola
[npm-downloads-href]: https://npmjs.com/package/consola

[package-phobia-src]: https://flat.badgen.net/packagephobia/install/consola
[package-phobia-href]: https://packagephobia.now.sh/result?p=consola

[bundle-phobia-src]: https://flat.badgen.net/bundlephobia/minzip/consola
[bundle-phobia-href]: https://bundlephobia.com/result?p=consola

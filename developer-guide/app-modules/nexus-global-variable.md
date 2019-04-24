# `NEXUS` global variable

`NEXUS` global variable is a javascript object injected into the global scope of every module's javascript execution environment. It contains all the libraries, data and methods that you need to be able to interact and communicate with the base wallet.

From your module's js code, you can access to `NEXUS` global variable via `window.NEXUS` or just `NEXUS`, for example:

```js
const React = NEXUS.libraries.React
```

Below are what `NEXUS` global variable provides you with:

## `specVersion`

The current version of [Nexus Wallet Module Specifications](../module-specifications.md) that Nexus Wallet is running.

```js
const { specVersion } = NEXUS
```

## `libraries`

`libraries` object contains the most commonly used third-party js libraries that's also used in the base wallet. If you use one or more of these libraries, you can grab them from `NEXUS.libraries` without having to add them as a dependency of your module, reducing your module's distributed package size.

```js
const {
  libraries: {
    React,          // from 'react'
    ReactDOM,       // from 'react-dom'
    ReactRouterDOM, // from 'react-router-dom
    Redux,          // from 'redux'
    ReactRedux,     // from 'react-redux'
    emotion: {
      core,         // from '@emotion/core'
      styled,       // from '@emotion/styled'
      theming,      // from 'emotion-themeing'
      createCache,  // from '@emotion/cache'
    },
    victory,        // from 'victory'
  }
} = NEXUS
```

## `utilities`

`utilities` provides you with the utility functions that might be useful for your module code.

```js
const {
  utilities: {
    color,
    copyToClipboard,
  }
} = NEXUS
```

[docs TBD]


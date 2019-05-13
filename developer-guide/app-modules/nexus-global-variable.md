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

---

## `libraries`

`libraries` object provides the most commonly used third-party js libraries that's also used in the base wallet. If you use one or more of these libraries, you can grab them from `NEXUS.libraries` without having to add them as a dependency of your module, reducing your module's distributed package size.

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

---

## `components`

`components` provides reusable React components that are used in the base wallet. Using these React components can help you shorten your development time by not having to reinvent the wheel, and make your module UI look and feel more in sync with the base wallet UI.

Please note that these components are built on React, so your module also need to be built on React in order to use them. These components are also styled using Emotion, so although you can use them without using Emotion, it would be easiest to customize them using Emotion.

```js
const {
  components: {
    GlobalStyles,
    Panel,
    Button,
    Tooltip,
    TextField,
    Switch,
    Select,
    Link,
    Icon,
    Tab,
    FieldSet,
  }
} = NEXUS
```

See [React components](./react-components.md) for more details.



---

## `utilities`

`utilities` object provides functions that helps your module interact with the base wallet, manipulate color values, copy text to clipboard, etc...

```js
const {
  utilities: {
    showNotification,
    showErrorDialog,
    showSuccessDialog,
    rpcCall,
    proxyRequest,
    confirm,
    updateState,
    updateStorage,
    onceInitialize,
    onThemeUpdated,
    onSettingsUpdate,
    onCoreInfoUpdated,
    onceRpcReturn,
    onceProxyResponse,
    onceCOnfirmAnswer,
    copyToClipboard,
    color,
  }
} = NEXUS
```

See [utilities](./utilities.md) for more details


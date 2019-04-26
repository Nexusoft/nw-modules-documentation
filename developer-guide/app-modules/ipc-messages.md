# IPC messages API

Since your module runs on a different process than the base wallet, in order to communicate with each other, your module and the base wallet need to send and receive **IPC (inter-process communication) messages**. Methods for sending and receiving IPC messages can be found in `NEXUS.ipc` object of the [`NEXUS` global variable](./nexus-globalvariable.md) (for example you can call `NEXUS.ipc.send(...)`). A few notes about IPC messages usage:

- IPC messages are asynchronous.
- All the arguments you pass in will be serialized in JSON, so functions or any other non-serializable data won't work.

## Methods

### `send(channel, ...args)`

- `channel`: string
- `args`: any[]

Send an IPC message from module to the base wallet via the specified `channel`.

### `listen(channel, listener)`

- `channel`: string
- `listener`: function

Listen to all IPC messages sent from the base wallet via the specified `channel`. 
Accepted arguments for the `listener` function depends on the `channel` you're listening to.

### `listenOnce(channel, listener)`

- `channel`: string
- `listener`: function

Listen to an IPC message sent from the base wallet via the specified `channel` just once, then the listener will be automatically removed.
Accepted arguments for the `listener` function depends on the `channel` you're listening to.

## Incoming channels (to modules)

### `initialize`

Usage:

```js
listenOnce('initialize', initialData => {
  const {
    theme,
    settings,
    coreInfo,
    moduleState,
    storageData
  } = initialData
  // do cool things...
})
```

`initialize` message will be sent only once when the `webview`'s DOM is ready. `initialize` message provides modules with necessary initial data, including:

#### `theme` 

The current theme object that the base wallet is using. It is best used in combination with `NEXUS.utilities.color.getMixer` and pass to [Emotion](https://emotion.sh)'s `ThemeProvider`:

```js
// Add the mixer function
const themeWithMixer = {
  ...theme,
  mixer: color.getMixer(theme.background, theme.foreground),
};

// Then render this...
<ThemeProvider theme={themeWithMixer}>
  ...
</ThemeProvider>
```

Check out usage example in [react-redux_module_example](https://github.com/Nexusoft/react_redux_module_example).

#### `settings`

The current user settings that the base wallet is using. It's not the full settings but only a few settings that modules might care about.

```js
// Fields in `settings`
{
  locale,       // string, e.g. 'en'
  fiatCurrency, // string, e.g. 'USD'
  addressStyle, // string enum: ['segmented', 'truncateMiddle', 'raw']
}
```

#### `coreInfo`

Information that the core returned from `getinfo` RPC calls. What's contained inside `coreInfo` depends on the core that the Nexus Wallet is using.

#### `moduleState`

The last state object that your module has previously stored via the `update-state` IPC message.

#### `storageData`

The last data object that your module has previously stored via the `update-storage` IPC message.
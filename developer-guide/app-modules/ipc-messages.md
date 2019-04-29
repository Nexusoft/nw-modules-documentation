# IPC messages API

Since your module runs on a different process than the base wallet, in order to communicate with each other, your module and the base wallet need to send and receive **IPC (inter-process communication) messages**. Methods for sending and receiving IPC messages can be found in `NEXUS.ipc` object of the [`NEXUS` global variable](./nexus-globalvariable.md) (for example you can call `NEXUS.ipc.send(...)`). A few notes about IPC messages Example usage:

- IPC messages are executed asynchronously, therefore the call results (if any) will not be returned directly from the function calls.
- All the arguments you pass in will be serialized in JSON, so functions or any other non-serializable data won't work.

Table of contents

- [Methods](#methods)
  - [`send(channel, ...args)`](#sendchannel-args)
  - [`listen(channel, listener)`](#listenchannel-listener)
  - [`listenOnce(channel, listener)`](#listenoncechannel-listener)
- [Outgoing channels (from modules)](#outgoing-channels-from-modules)
  - [`rpc-call` channel](#rpc-call-channel)
- [Incoming channels (to modules)](#incoming-channels-to-modules)
  - [`initialize` channel](#initialize-channel)
  - [`theme-updated` channel](#theme-updated-channel)
  - [`settings-updated` channel](#settings-updated-channel)
  - [`core-info-updated` channel](#core-info-updated-channel)
  - [`rpc-return[:id]` channel](#rpc-returnid-channel)

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

## Outgoing channels (from modules)

### `rpc-call` channel

Sends an [RPC call](https://en.wikipedia.org/wiki/Remote_procedure_call) to the core. The call result (or error) will be sent back to module via `rpc-return` channel.

```js
send(`rpc-call`, options: object)
```

Available options:

- `command`: string - A valid command that will be sent to Nexus core (see Nexus core documentation for list of all available commands).
- `params`: array - List of all params that will be passed along with the command.
- `callId`: number|string (optional) - An optional identifier for identifying the call. If `callId` is not provided, the call result will be sent back via [`rpc-return` channel](#rpc-returnid-channel). If a non-falsy `callId` value is provided, the call result will be sent back via `rpc-return:<id>` channel.

Example usage:

```js
NEXUS.ipc.send(`rpc-call`, {
  command: 'getaccountaddress',
  params: ['default'],
  callId: 1
})
```

## Incoming channels (to modules)

### `initialize` channel

Example usage:

```js
NEXUS.ipc.listenOnce('initialize', initialData => {
  const {
    theme,
    settings,
    coreInfo,
    moduleState,
    storageData
  } = initialData
  // populate initial data in module...
})
```

`initialize` message is sent only once when the `webview`'s DOM is ready. `initialize` message provides modules with necessary initial data, including:

- `theme` 

  The current theme object that the base wallet is using. It is best used in combination with `NEXUS.utilities.color.getMixer` and pass to [Emotion](https://emotion.sh)'s `ThemeProvider`:

  ```js
  // Add the mixer function
  const themeWithMixer = {
    ...theme,
    mixer: color.getMixer(theme.background, theme.foreground),
  };

  // Then render this...
  <ThemeProvider theme={themeWithMixer}>
    {/* Your module... */}
  </ThemeProvider>
  ```

Check out usage example in [react-redux_module_example](https://github.com/Nexusoft/react_redux_module_example).

- `settings`

  The current user settings that the base wallet is using. It's not the full settings but only a few settings that modules might care about.

  ```js
  // Fields in `settings`
  {
    locale,       // string, e.g. 'en'
    fiatCurrency, // string, e.g. 'USD'
    addressStyle, // string enum: ['segmented', 'truncateMiddle', 'raw']
  }
  ```

- `coreInfo`

  Information that the core returned from `getinfo` RPC calls. What's contained inside `coreInfo` depends on the core that the Nexus Wallet is using.

- `moduleState`

  The last state object that your module has previously stored via the `update-state` IPC message.

- `storageData`

  The last data object that your module has previously stored via the `update-storage` IPC message.

### `theme-updated` channel

Example usage:

```js
NEXUS.ipc.listen('theme-updated', theme => {
  // update theme in module...
})
```

`theme-updated` message is sent everytime the wallet theme is changed in the base wallet.

### `settings-updated` channel

Example usage:

```js
NEXUS.ipc.listen('settings-updated', settings => {
  // update settings in module...
})
```

`settings-updated` message is sent everytime the settings is changed in the base wallet.

### `core-info-updated` channel

Example usage:

```js
NEXUS.ipc.listen('core-info-updated', coreInfo => {
  // update core info in module...
})
```

`core-info-updated` message is sent everytime the core info is updated in the base wallet.

### `rpc-return[:id]` channel

`rpc-return` message is sent back to your module as the result of a previous [`rpc-call`](#rpc-call-channel) message that your module has sent to the base wallet.

If the previous `rpc-call` did not include a valid `callId` option, the result channel will be just `rpc-return`.

If the previous `rpc-call` did include a valid `callId` option, the result channel will be just `rpc-return:<id>`. For example if you call `send('rpc-call', { callId: 10, ... }`, you should then call `listenOnce('rpc-return:10', (err, result) => { ... })`.

Example usage:

```js
const callId = generateUniqueId()
NEXUS.ipc.listenOnce(`rpc-return:${callId}`, (err, result) => {
  if (err) {
    // handle error...
  } else {
    // handle result
  }
})
NEXUS.ipc.send('rpc-call', {
  callId,
  // other options...
})
```


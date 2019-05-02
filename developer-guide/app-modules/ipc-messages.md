# IPC messages API

Since your module runs on a different process than the base wallet, in order to communicate with each other, your module and the base wallet need to send and receive **IPC (inter-process communication) messages**. Methods for sending and receiving IPC messages can be found in `NEXUS.ipc` object of the [`NEXUS` global variable](./nexus-globalvariable.md) (for example you can call `NEXUS.ipc.send(...)`). A few notes about IPC messages Example usage:

- IPC messages are executed asynchronously, therefore the call results (if any) will not be returned directly from the function calls.
- All the arguments you pass in will be serialized in JSON, so functions or any other non-serializable data won't work.

### Table of contents

- [Methods](#methods)
  - [`send(channel, ...args)`](#sendchannel-args)
  - [`listen(channel, listener)`](#listenchannel-listener)
  - [`listenOnce(channel, listener)`](#listenoncechannel-listener)
- [Outgoing channels (from modules)](#outgoing-channels-from-modules)
  - [`show-notification` channel](#show-notification-channel)
  - [`show-error-dialog` channel](#show-error-dialog-channel)
  - [`show-success-dialog` channel](#show-success-dialog-channel)
  - [`rpc-call` channel](#rpc-call-channel)
  - [`confirm` channel](#confirm-channel)
  - [`update-state` channel](#update-state-channel)
- [Incoming channels (to modules)](#incoming-channels-to-modules)
  - [`initialize` channel](#initialize-channel)
  - [`theme-updated` channel](#theme-updated-channel)
  - [`settings-updated` channel](#settings-updated-channel)
  - [`core-info-updated` channel](#core-info-updated-channel)
  - [`rpc-return[:id]` channel](#rpc-returnid-channel)
  - [`confirm-answer[:id]` channel](#confirm-answerid-channel)

---

## Methods

### `send(channel, ...arguments)`

- `channel`: string
- `arguments`: any[]

Send an IPC message from module to the base wallet via the specified `channel`.
Specific types and number of `arguments` depends on the `channel` you're sending to.

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

---

## Outgoing channels (from modules)

### `show-notification` channel

Displays a notification at the top left corner of the wallet.

```js
send(`show-notification`, options: object)
```

Available options:

- `content`: string - The content that you want to display in the notification.
- `type`: string (optional) - Type of notification that you want to display. Available types are: `info` (default), `success`, `error`.
- `autoClose`: number (optional) - Number of miliseconds after which you want the notification to automatically close. If a falsy value is passed (except `undefined`), the notification can only be closed manually. Default value is `3000`.

### `show-error-dialog` channel

Displays an error dialog in the wallet.

```js
send(`show-error-dialog`, options: object)
```

Available options:

- `message`: string - The main (larger) text shown in the error dialog.
- `note`: string (optional) - The supporting (smaller) text shown in the error dialog below the `message`.

### `show-success-dialog` channel

Displays an success dialog in the wallet.

```js
send(`show-success-dialog`, options: object)
```

Available options:

- `message`: string - The main (larger) text shown in the success dialog.
- `note`: string (optional) - The supporting (smaller) text shown in the success dialog below the `message`.

### `rpc-call` channel

Sends an [RPC call](https://en.wikipedia.org/wiki/Remote_procedure_call) to the core. 

The call result (or error) will be sent back to module via [`rpc-return[:id]` channel](#rpc-returnid-channel).

```js
send(`rpc-call`, options: object)
```

Available options:

- `command`: string - A valid command that will be sent to Nexus core (see Nexus core documentation for list of all available commands).
- `params`: array - List of all params that will be passed along with the command.
- `callId`: number\|string (optional) - An optional identifier for identifying the call. If `callId` is not provided, the call result will be sent back via `rpc-return` channel. If a non-falsy `callId` value is provided, the call result will be sent back via `rpc-return:<callId>` channel.

Example usage:

```js
NEXUS.ipc.send(`rpc-call`, {
  command: 'getaccountaddress',
  params: ['default'],
  callId: 1
})
```

### `confirm` channel

Displays a confirmation dialog to the user. The confirmation dialog contains a question and two buttons for "Yes" and "No" answers.

The answer user chooses (either `true` or `false`) will be sent back to module via [`confirm-answer[:id]` channel](#confirm-answerid-channel).

```js
send(`confirm`, options: object)
```

Available options:

- `question`: string - The question (on larger text) to display on the confirmation dialog.
- `note`: string (optional) - The added information (on smaller text) to display on the confirmation dialog under the question.
- `labelYes`: string (default: `'Yes'`) - The custom label for the "Yes" button, which will send back the result `true` when chosen by user.
- `skinYes`: string (default: `'primary'`) - The button skin for the "Yes" button. List of available values for button skin can be found here (coming soon).
- `labelNo`: string (default: `'No'`) - The custom label for the "No" button, which will send back the result `false` when chosen by user.
- `skinNo`: string (default: `'default'`) - The button skin for the "No" button. List of available values for button skin can be found here (coming soon).
- `confirmationId`: number\|string (optional) - An optional identifier for identifying the confirmation. If `confirmationId` is not provided, the answer will be sent back via `confirm-answer` channel. If a non-falsy `confirmationId` value is provided, the answer will be sent back via `confirm-answer:<confirmationId>` channel.

Example usage:

```js
NEXUS.ipc.send(`rpc-call`, {
  command: 'getaccountaddress',
  params: ['default'],
  callId: 1
})
```

### `update-state` channel

Saves an arbitrary data object (usually your module's state data) into the base wallet's memory so that it won't be lost when user navigates away from your module.

Because all your module's code is embedded inside a [`<webview>` tag](./README.md#webview-tag), normally when user navigates away from your module page, the `<webview>` tag will be unmounted and all your module state will be lost. The next time user navigates back to your module, user will have to do everything from the beginning. Therefore you might want to save your module's state into the base wallet by interval or everytime when it's changed.

With the `update-state` IPC message, the next time user navigates back to your module, the **last** data object that you've sent along with the `update-state` message will be passed back to you module in the `moduleState` object via the [`initialize` channel](#initialize-channel).

```js
send(`update-state`, state: object)
```

`state` is any data object that you want to save.

Note: This data will not be persisted when the wallet is closed. In order to persist data even when the wallet is closed, use `update-storage` channel.

---

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

  The last state object that your module has previously stored via the [`update-state` channel](#update-state-channel).

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

`rpc-return` message is sent to your module as the result of a previous [`rpc-call`](#rpc-call-channel) message that your module has sent to the base wallet.

If the previous `rpc-call` IPC message did not include a valid `callId` option, the result channel would be just `rpc-return`.

If the previous `rpc-call` IPC message did include a valid `callId` option, the result channel would be `rpc-return:<callId>`. For example if you call `send('rpc-call', { callId: 10, ... }`, you should then call `listenOnce('rpc-return:10', (err, result) => { ... })`.

Example usage:

```js
const callId = generateUniqueId()
NEXUS.ipc.listenOnce(`rpc-return:${callId}`, (err, result) => {
  if (err) {
    // handle error...
  } else {
    // handle result...
  }
})
NEXUS.ipc.send('rpc-call', {
  callId,
  // other options...
})
```

### `confirm-answer[:id]` channel

`confirm-answer` message is sent to your module as the result of a previous [`confirm`](#confirm-channel) message that your module has sent to the base wallet.

If the previous `confirm` IPC message did not include a valid `confirmationId` option, the result channel would be just `confirm-answer`.

If the previous `confirm` IPC message did include a valid `confirmationId` option, the result channel would be `confirm-answer:<confirmationId>`. For example if you call `send('confirm', { confirmationId: 14, ... }`, you should then call `listenOnce('confirm-answer:14', (err, result) => { ... })`.

Example usage:

```js
const confirmationId = generateUniqueId()
NEXUS.ipc.listenOnce(`confirm-answer:${confirmationId}`, agreed => {
  if (agreed) {
    // proceed...
  } else {
    // cancel the action...
  }
})
NEXUS.ipc.send('confirm', {
  confirmationId,
  // other options...
})
```


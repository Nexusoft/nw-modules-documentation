# Utilities

Notes about the utilities function

- The arguments you pass in will be serialized in JSON, so passing functions or any other non-serializable data won't work.
- Most functions are executed asynchronously (except for the `color` utilities). In those cases, call results are not returned directly from the function calls but can only be received by registering a listener with the corresponding utility function. 

### Utilities API references

- [`showNotification`](#showNotification)
- [`showErrorDialog`](#showErrorDialog)
- [`showSuccessDialog`](#showSuccessDialog)
- [`updateState`](#updateState)
- [`updateStorage`](#updateStorage)
- [`onceInitialize`](#onceInitialize)
- [`onThemeUpdated`](#onThemeUpdated)
- [`onSettingsUpdate`](#onSettingsUpdate)
- [`onCoreInfoUpdated`](#onCoreInfoUpdated)
- [`rpcCall` and `onceRpcReturn`](#rpcCall-and-onceRpcReturn)
- [`proxyRequest` and `onceProxyResponse`](#proxyRequest-and-onceProxyResponse)
- [`confirm` and `onceConfirmAnswer`](#confirm-and-onceConfirmAnswer)
- [`copyToClipboard`](#copyToClipboard)
- [`color`](#color)

---

### `showNotification`

Displays a notification at the top left corner of the wallet.

```js
showNotification(options: object)
```

Available options:

- `content`: string - The content that you want to display in the notification.
- `type`: string (optional) - Type of notification that you want to display. Available types are: `info` (default), `success`, `error`.
- `autoClose`: number (optional) - The time (in miliseconds) after which the notification will automatically be closed. If a falsy value except `undefined` is passed, the notification will not automatically be closed. Default value is `5000`.

### `showErrorDialog` 

Displays an error dialog in the wallet.

```js
showErrorDialog(options: object)
```

Available options:

- `message`: string - The main (larger) text shown in the error dialog.
- `note`: string (optional) - The supporting (smaller) text shown in the error dialog below the `message`.

### `showSuccessDialog`

Displays an success dialog in the wallet.

```js
showSuccessDialog(options: object)
```

Available options:

- `message`: string - The main (larger) text shown in the success dialog.
- `note`: string (optional) - The supporting (smaller) text shown in the success dialog below the `message`.

### `updateState`

Saves an arbitrary data object (usually your module's state data) into the base wallet's memory so that it won't be lost when user navigates away from your module.

Because all your module's code is embedded inside a [`<webview>` tag](./README.md#webview-tag), normally when user navigates away from your module page, the `<webview>` tag will be unmounted and all your module state will be lost. The next time user navigates back to your module, user will have to do everything from the beginning. Therefore you might want to save your module's state into the base wallet by interval or everytime when it's changed.

Using the `updateState` utility, the next time user navigates back to your module, the **last** data object that you've saved with `updateState` will be passed back to your module via [`onceInitialize` listener](#onceInitialize).

```js
updateState(state: object)
```

- `state`: object - Any data object that you want to save.

Note: This data will not be persisted when the wallet is closed. In order to persist data even when the wallet is closed, use [`updateStorage` utilities](#updateStorage) instead.

### `updateStorage` channel

Saves an arbitrary data object (usually your module's settings) into a file so that it won't be lost when the wallet is closed.

Data will be saved into a file named `storage.json` inside your module's directory, therefore each module has its own storage, not shared with any other. Maximum size of the data that can be stored in `storage.json` is roughly 1MB.

Using the `updateStorage` utility, the next time user navigates back to your module, the **last** data object that you've saved with `updateStorage` will be passed back to your module via [`onceInitialize` listener](#onceInitialize).

```js
send(`updateStorage`, data: object)
```

- `data`: object - Any data object that you want to save.

Note: This will write data into a file on user's hard drive, so avoid calling this on highly frequent events such as on user's key stroke. For the data that doesn't need to be persisted when the wallet is closed (textbox content for example), use [`updateState` utilities](#updateState) instead.

### `onceInitialize`

Register a listener that receives the initial data passed from the base wallet.

The listener registered in `onceInitialize` will be called only once when the `webview`'s DOM is ready.

```js
const listener = initialData => {
  const {
    theme,
    settings,
    coreInfo,
    moduleState,
    storageData
  } = initialData
  // populate initial data in module...
}
NEXUS.utilities.onceInitialize(listener)
```

- initialData: object

  - `theme`: object - See [`onThemeUpdated`](#onThemeUpdated) for more details

  - `settings`: object - See [`onSettingsUpdated`](#onSettingsUpdated) for more details

  - `coreInfo`: object - See [`onCoreInfoUpdated`](#onCoreInfoUpdated) for more details

  - `moduleState`: object - The last state object that your module has previously stored via the [`updateState` utility](#updateState).

  - `storageData`: object - The last data object that your module has previously stored via the [`updateStorage` utility](#updateStorage).

### `onThemeUpdated`

Example usage:

`onThemeUpdated` listener is called everytime the wallet theme is changed in the base wallet.

```js
const listener = theme => {
  // update theme in module...
}
NEXUS.utilities.onThemeUpdated(listener)
```

- `theme`: object - The current theme object that the base wallet is using. It is best used in combination with `NEXUS.utilities.color.getMixer` and pass to [Emotion](https://emotion.sh)'s `ThemeProvider`:

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

### `onSettingsUpdated`

`onSettingsUpdated` listener is called everytime the settings is changed in the base wallet.

```js
const listener = settings => {
  // update settings in module...
}
NEXUS.utilities.onSettingsUpdated(listener)
```

- `settings`: object - The current user settings that the base wallet is using. It's not the full settings but only a few settings that modules might care about.

  ```js
  // Fields in `settings`
  {
    locale,       // string, e.g. 'en'
    fiatCurrency, // string, e.g. 'USD'
    addressStyle, // string enum: ['segmented', 'truncateMiddle', 'raw']
  }
  ```

### `onCoreInfoUpdated` channel

`onCoreInfoUpdated` listener is called everytime the core info is updated in the base wallet.

```js
const listener = coreInfo => {
  // update core info in module...
}
NEXUS.utilities.onCoreInfoUpdated(listener)
```

- `coreInfo`: object - Information that the Nexus core returned from `getinfo` RPC calls which are called at regular interval. What's contained inside `coreInfo` depends on the core that the Nexus Wallet is using.

### `rpcCall` and `onceRpcReturn`

`rpcCall` sends an [RPC call](https://en.wikipedia.org/wiki/Remote_procedure_call) to Nexus core, and `onceRpcReturn` gets you the result.

`onceRpcReturn` will be called only once for each `rpcCall` call.

```js
rpcCall(command: string, params: array, callId: number|string)
```

- `command`: string - A valid command that will be sent to Nexus core (see Nexus core documentation for list of all available commands).
- `params`: array - List of all params that will be passed along with the command.
- `callId`: number\|string - An **unique** identifier for the call. `rpcCall`'s result will be passed to the corresponding `onceRpcReturn` listener which passed the same `callId`.

```js
onceRpcReturn(listener: function, callId: number|string)
```

- `listener`: function(err: any, result: any) - This function will be called when the RPC call completes either with `err` or `result`.
- `callId`: number\|string - The **unique** identifier for the call that was passed to the corresponding `rpcCall` call.

Example usage:

```js
const callId = generateUniqueId()
const listener = (err, result) => {
  if (err) {
    // handle error...
  } else {
    // handle result...
  }
}
NEXUS.utilities.onceRpcReturn(listener, callId)
NEXUS.utilities.rpcCall('getaccountaddress', ['default'], callId)
```

### `confirm` and `onceConfirmAnswer`

`confirm` displays a confirmation dialog to the user. The confirmation dialog contains a question and two buttons for "Yes" and "No" answers, and `onceConfirmAnswer` gets you the answer user selected (either `true` for "Yes" or `false` for "No").

`onceConfirmAnswer` will be called only once for each `confirm` call.

```js
confirm(options: object, confirmationId: number|string)
```

- `options`: object - Available options:
  - `question`: string - The question (on larger text) to display on the confirmation dialog.
  - `note`: string (optional) - The added information (on smaller text) to display on the confirmation dialog under the question.
  - `labelYes`: string (default: `'Yes'`) - The custom label for the "Yes" button, which will send back the result `true` when chosen by user.
  - `skinYes`: string (default: `'primary'`) - The button skin for the "Yes" button. List of available values for button skin can be found here (coming soon).
  - `labelNo`: string (default: `'No'`) - The custom label for the "No" button, which will send back the result `false` when chosen by user.
  - `skinNo`: string (default: `'default'`) - The button skin for the "No" button. List of available values for button skin can be found here (coming soon).
- `confirmationId`: number\|string - An **unique** identifier for the confirmation. `confirm`'s result will be passed to the corresponding `onceConfirmAnswer` listener which passed the same `confirmationId`.

```js
onceConfirmAnswer(listener: function, confirmationId: number|string)
```

- `listener`: function(agreed: boolean) - This function will be called when user selects the answer - `true` if user selected "Yes" or `false` if user selected "No".
- `confirmationId`: number\|string - The **unique** identifier for the confirmation that was passed to the corresponding `confirm` call.

Example usage:

```js
const confirmationId = generateUniqueId()
const listener = agreed => {
  if (agreed) {
    // proceed...
  } else {
    // cancel the action...
  }
}
NEXUS.utilities.onceConfirmAnswer(listener, confirmationId)
NEXUS.utilities.confirm({ /* options... */ }, confirmationId)
```
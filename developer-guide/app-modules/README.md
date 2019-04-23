# `app` module type

`app` is the first and only Nexus Wallet Module type that's currently supported. When an `app` module is installed in your wallet, its icon will be added to the navigation bar at the bottom of the wallet, and when activated it opens up a separate view in your wallet, similarly to how the built-in modules like Transactions or Settings do.

## `<webview>`

On the technical side, that module's view is embedded inside an [Electron](https://electronjs.org/)'s [`<webview>` tag](https://electronjs.org/docs/api/webview-tag). `<webview>` ensures that the module code executing environment is isolated from the base wallet for better security.

Since the module code is isolated inside the `<webview>`, it needs some ways to communicate with the base wallet to do useful things, such as to receive wallet data, do RPC calls, or get the common styles and current theme from the base wallet so that the module UI blends well into the wallet UI around. [`NEXUS` global variable](./nexus-globalvariable.md) exists for that reason. It is the bridge between your module and the base wallet, provides you all the necessary libraries, utilities, common components, and methods for interacting with the base wallet. See [`NEXUS` global variable](./nexus-globalvariable.md) for more details.
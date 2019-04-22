# `app` module type

`app` is the first and only Nexus Wallet Module type that's currently supported. When an `app` module is installed in your wallet, its icon will be added to the navigation bar at the bottom of the wallet, and when activated it opens up a separate view in your wallet, almost similarly to how the built-in modules like Transactions or Settings do.

On the technical side, that module's view is embedded inside an [Electron](https://electronjs.org/)'s [`<webview>` tag](https://electronjs.org/docs/api/webview-tag). `<webview>` tag ensures that the module code executing environment is isolated from the base wallet for better security.

Since the module code is isolated, it needs some ways to communicate with the base wallet. 
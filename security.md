# Module security

Nexus Wallet Modules can be written by any third-party developers, therefore security is one of our primary concerns when we design the module system. 

## `webview` security

Code inside `webview` is executed in a separate process from the base wallet, therefore there is very few things that module code can do to maliciously affect the base wallet.

Module's entry HTML is fetched from a static file server running locally (e.g. `http://localhost:9331/...`) that only serves exactly the files listed in [`files` field of `nxs_package.json`](./developer-guide/nxs_package.json.md#files) so it is not possible for modules to retrieve private files in user's computer via `file://` protocol.

For [IPC messages](./developer-guide/app-modules/ipc-messages.md), all data will be serialized in JSON, so functions won't work, and it's NOT possible for modules to pass malicious code to the base wallet.

### A note about Electron's warning

You may have noticed, in [Electron's documentation on `<webview>`](https://electronjs.org/docs/api/webview-tag) there is a warning about `<webview>` tag's stability. However, the warning only addresses `webview`'s possible changes in architecture and API in the future, not security problems. This has already been [confirmed by Electron team](https://github.com/electron/electron/issues/18187). Architectural and API changes are not really big concerns for us because we control when and whether to update Electron version, and can decide to hold off the updates until we can adapt to the changes. So , it is safe to use `<webview>` tag.

## SVG icon security

Module SVG icon's security is a concern, because unlike the rest of the module code, which is isolated in a `webview`, module icon is embedded directly in the base wallet, and SVG icons, by nature are XML files and can contain code. Therefore, we use [DOMPurify](https://github.com/cure53/DOMPurify) library to sanitize module SVG icons before rendering, making sure there are no dirty pieces of SVG can make their way into the HTML.

## Module open source policy

Another layer of security that we have created to minimize the risk that a module can contain malicious code is the Module open source policy. 

This policy requires all the modules to have a publicly accessible source code repository in order to be installed and run on Nexus Wallet. So in case someone has found a security hole that they can exploit (which is expected to be relatively hard already), they would also have to hope that neither the Nexus team wouldn't detect the malicious code during the [Repository verification process](./developer-guide/repo-verification) and reject. Even in case the Nexus team doesn't detect the malicious code and let them distribute their module, they would still have to hope that no one else in the whole community discover it while their source code must be kept public for anyone to read.

When this policy is turned on (it is by default), Nexus Wallet will check for the following conditions everytime the wallet loads modules or user tries to install a new module:

- There must be a valid [`repo_info.json` file](./repo_info.json.md) included in the module directory with a valid `RSA-SHA256` signature from Nexus team. 

- The repository must be publicly accessible - this will be checked by calling a github API.
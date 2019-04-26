# nxs_package.json

`nxs_package.json` is a mandatory file that **all** Nexus modules **must** contain, and must be located in the top-level directory of the module.

`nxs_package.json` contains most meta-data about the module, and helps Nexus Wallet identify if a directory is a module or not.

As the name suggests, `nxs_package.json` is inspired by [`npm`'s `package.json`](https://docs.npmjs.com/files/package.json) file, plays a pretty similar role in Nexus modules as `package.json` does in `npm` packages, and has many similar fields as in `package.json`.

## Schema

### `name`

- **Mandatory**
- Type: `string`
- Constraints: only accepts lowercase letters, digits, underscores (`_`) and dashes (`-`)

This will be used as the unique identifier of your module. If more than one modules with the same `name` are installed in a wallet, only one of them will work. Currently there hasn't been a central "module store" that hosts all the Nexus modules available, avoiding naming collision must be taken care by the module developers yourself. So when you need to name your module, please be aware if there is already an existing module with the same name.

### `displayName`

- **Mandatory**
- Type: `string`
- Constraints: no newline characters

A user-friendly name to be displayed on the GUI and most other places where your module name is needed.

Should not be too different from the `name` field.

### `version`

- **Mandatory**
- Type: `string`
- Constraints: must be a valid [semver](https://semver.org/) version

The version of your module.

### `specVersion`

- **Mandatory**
- Type: `string`
- Constraints: must be a valid [semver](https://semver.org/) version

The version of the [Nexus Module Specifications](./nexus-module-specifications) that this version of your module was built on.

If this `specVersion` is lower than the lowest Spec version that user's wallet supports, your module will be marked "invalid" and will not work.

### `type`

- **Mandatory**
- Type: `string`
- Constraints: possible values are `app` (the only supported module type currently)

The type of your module. This decides how your module will be used in the wallet. See [Module types](./module-types.md) for more details.

### `options`

- Optional
- Type: `object`
- Constraints:
  - Available options:
```
{
  // Whether the module's webview is wrapped inside a panel
  // Only available with `app` type modules
  wrapInPanel: boolean
}
```

### `files`

- **Mandatory**
- Type: `string[]`
- Constraints:

  - All elements are [internal file paths](#internal-file-paths)
  - Cannot have the same path with one of the following files: `nxs_package.json`, `repo_info.json`, `storage.json`

This is where you should list ALL the files needed to run your module (without wildcards). Only those files that present in this list will be copied over when users install your module their wallets, so please don't miss any file.

### `description`

- Optional
- Type: `string`
- Constraints: none

A brief description about your module.

### `entry`

- Optional
- Type: `string`
- Constraints: [internal file paths](#internal-file-paths)

Path to the entry file of your module. The type of entry file depends on the module type. If your module is of type `app`, your `entry` file must be in `.html` format. See [HTML entry file](./app-modules#html-entry-file) section for more details.

### `icon`

- Optional
- Type: `string`
- Constraints: [internal file paths](#internal-file-paths)

Path to the icon of you module. See [Module icon](./module-icon.md) for more details.

### `author`

- Optional
- Type: `object`
- Constraints:
  - Object schema:
```
{
  name: string,
  email: string
}
```
  - `name` field is required

Information about the module's author.

## Commonly used constraints

### Internal file paths

- Must be relative paths, absolute paths are not allowed.
- Wildcards are not allowed.
- Parent directory notations (`../`) are not allowed.

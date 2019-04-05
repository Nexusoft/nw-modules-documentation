# nxs_package.json

`nxs_package.json` is a mandatory file that __all__ Nexus modules __must__ contain, and must be located in the top-level directory of the module.

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

Should not be way too different from the `name` field.

### `version`

- **Mandatory**
- Type: `string`
- Constraints: must be a valid [semver](https://semver.org/) version

The version of your module.

### `specVersion`

- **Mandatory**
- Type: `string`
- Constraints: must be a valid [semver](https://semver.org/) version

The version of the [Nexus module specification](./nexus-module-specification) that this version of your module was built on.

If this `specVersion` is lower than the lowest Nexus Module Specification version that user's wallet supports, your module will be marked "invalid" and will not work.

### `type`

- **Mandatory**
- Type: `string`
- Constraints: enumerable [`page`, `page-panel`]

The type of your module. This decides how your module will be used in the wallet.

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

Path to the entry file of your module. The type of entry file depends on the module type, for example, if your module is of type `page` or `page-panel`, your `entry` file must be in `.html`.

If the `entry` field is not specified, the wallet will look for the default entry file name depending on the module type.

### `icon`

- Optional
- Type: `string`
- Constraints: [internal file paths](#internal-file-paths)

Path to the icon of you module. Currently there are 2 formats `.svg` and `.png` are supported for module icons.

If the `entry` field is not specified, by default the wallet will look for a file named `icon.svg`, if still not found, it will look for `icon.png` (both in the top level directory of your module). If none of the above are found, a default icon will be used.

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
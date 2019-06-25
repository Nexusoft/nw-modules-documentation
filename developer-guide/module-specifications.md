# Module Specifications

**Module Specifications** refers to the collection of all the interaction rules and APIs between the base Nexus Wallet and Nexus Wallet Modules. Some examples components of Module Specifications:

- File schemas - including schemas for [nxs_package.json](./nxs_package.json.md) and [repo_info.json](./repo_info.json.md).
- [NEXUS global variable](./app-modules/nexus-global-variable.md).

## Specifications version

**Nexus Wallet Module Specifications version** (or **spec version** for short) is independent from the Nexus Wallet version. Sometimes Nexus Wallet gets updated and has a new version but Nexus Wallet Module Specifications remains unchanged and its version stays the same.

In your module, you can get the current spec version of user's wallet from the `specVersion` field of the [NEXUS global variable](./injected-global-variable.md).

Any version of Nexus Wallet has:

- A current version of Nexus Wallet Module Specifications which is the newest by the time this version of Nexus Wallet is released.

- A lowest supported version of Nexus Wallet Module Specifications, which is still compatible with the current wallet. Any modules that is built on a Spec version that is lower than this is marked invalid and cannot be installed.

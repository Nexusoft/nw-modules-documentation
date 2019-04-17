# Nexus Module Specifications

**Nexus Module Specifications** (or just **Specifications** for short) refers to the collection of all the rules and interfaces for interaction between the base Nexus wallet and Nexus modules. Parts of Nexus module specifications include:

- File schemas - including schemas for [nxs_package.json](./nxs_package.json.md) and [repo_info.json](./repo_info.json.md).
- [Injected global variable](./injected-global-variable.md).
- [IPC messages API](./ipc-messages-api.md).

## Specifications versions

Nexus Module Specifications versions (or just **Spec versions**) are independent from the Nexus Wallet versions. Sometimes Nexus Wallet gets updated and has a new version but Nexus Module Specification remains unchanged and its version stays the same.

In your module, you can get the current Spec version of user's wallet from the `specVersion` field of the [injected global variable](./injected-global-variable.md).

Any version of Nexus Wallet has:

- A current version of Nexus Module Specifications which is the newest by the time this version of Nexus Wallet is released.
- A lowest supported version of Nexus Module Specifications, which is still compatible with the current wallet. Any modules that is built on a Spec version that is lower than this is marked invalid and cannot be installed.

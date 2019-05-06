# Repository verification

**Important note: Repository verification ONLY verifies that your module distribution matches the provided source code repository. Repository verification does NOT verifies that the module is clean, or does not contain malicious/faulty code.**

Repository verification is a mandatory step that you must take after you've completed developing your module and before you can distribute your module to Nexus Wallet users. 

Everytime you release new version of your module, you must go though the repository verification process again.

## Repository verification process

Before getting your repository verified, please ensure these prerequisites conditions are met:

- Your source code is hosted in a publicly accessible repository on [github.com](https://github.com/) (which is the only repository hosting that is currently supported). Private repositories won't work.

- Your source code can be built without errors, and you have written a clear and easy-to-follow build instruction on the main README of your repository. IF your module doesn't need to be built/compiled, it should also be stated clearly on the README.

- You have included a well-formed and valid [nxs_package.json](./nxs_package.json.md) in your repository.

When all the above prerequisites are met, send an email to [TBD] along with the following information:

1. An archive file (preferably in `.zip` format) that contains your module distribution (files that you'll distribute to users). This should be attached to the email.

3. The URL to your online repository AND the full hash of the specific commit in your repository that you compiled your module from (this will usually be the latest commit).

When the verification process completes successfully, you will receive an email with a [repo_info.json](./repo_info.json.md) that contains a signature from Nexus team. You should then copy this `repo_info.json` file into the root directory of your module before distributing, so that your module becomes valid and can be installed by Nexus Wallet users.

Please note that although this verification doesn't verify if your module contains malicious/faulty code or not, **the Nexus team still has the sole right to reject your application or to request for changes if they detect something bad or undesirable in the code**.
# Nexus Wallet Module Developer Guide

In this guide, you will find the instructions on how to write your own [Nexus Wallet Modules](../README.md).

## Quick Start

If you want to quickly set up your new module code base, you can check out some [examples and boilerplates](#examples-and-boilerplates), fork one of those repositories, and modify the code to achieve the functionality however you'd like.

## What programming languages can I use to build my modules?

Nexus Wallet is a web-based desktop application built on [Electron](https://electronjs.org/), therefore Nexus Wallet Modules can also be built using the **web technology**, i.e. HTML, CSS, and Javascript. You, as a module developer, have the total freedom to choose what web frameworks or libraries you like to use to build their modules, for example you can use either React, Angular or Vue for UI logic, LESS, SASS, or Stylus for styling,... or you can even just use plain HTML/CSS/Javascript if that's what you prefer.

However, if you aim for the best convenience, compatibility, and the most consistent look and feel compared to the base wallet, it is highly recommended that you use the same libraries (not necessarily all) that are used in the base wallet, namely:

- [React](https://reactjs.org/) for javascript GUI library.
- [Redux](https://redux.js.org/) for app state manager.
- [Emotion](https://emotion.sh) for CSS styling.
- [ReactRouter](https://reacttraining.com/react-router/) for app routing solution.

If you use the libraries listed above in your module, you won't need to add them to your `package.json` dependencies, because the base Nexus Wallet will pass these libraries to your module through an injected global variable, along with some utility functions and components built on these libraries (mostly React and Emotion). As a result, you will be able to reduce your module size by not having to include these libraries in your module, speed up your development by not having to reinvent the wheel, and ensure a more consistent UI with the base wallet by reusing the same UI components that the base wallet uses.

## What is required for a module to be valid?

A module (either in a directory or packaged as an archive) is considered valid if it meets all the requirements below:

1. A well-formed and valid [`nxs_package.json` file](./nxs_package.json.md) in the top level of your module directory.
2. A well-formed and valid [`repo_info.json` file](./repo_info.json.md) in the top level of your module directory.
3. The repository specified in `repo_info.json` must exist and publicly accessible (non-private).

If your module is not valid, Nexus Wallet users **will not be able to install it**. So you should ensure all the above conditions are satisfied before distributing your module.

If somehow a user has already installed your module, but your module becomes invalid at the moment user opens the wallet (for example your repository is then deleted), your module will not be loaded.

## Module types

Module types are defined in the `type` field of each module's [nxs_package.json](./nxs_package.json.md) file. Modules may interact with the base wallet differently depending on the module types. 

At the moment there is only one supported module type that is [`app`](./app-modules). More types, such as `theme`, will likely be added in the future.

## Get your repository verified

When you've completed your module development, you need to get your source code repository verified before you can distribute your module to users. This verification process will check if the repository you provide is truly the source code of the module package you distribute. See [Repository verification](./repo-verification.md) for more details.

## Packaging

You can compress your module in either `.zip` or `.tar.gz` formats so users will be able to install it directly from those archive files. You can also compress your files in other archive formats, though in that case, users will have to manually extract your compressed module first, then choose to install from the extracted directory.

## Delivering

You are free to choose how to deliver your module to your users, either uploading your packaged modules to your website or sending them directly via chat or forum, etc...

One easy way that we recommend is to [create a release on github](https://help.github.com/en/articles/creating-releases), then send the link to your module's release page via your blogs, social media, chat, and other channels that you use.

## Examples and boilerplates

Currently there are 3 Nexus Wallet Module example repositories that you can also use as boilerplates:

- [minimal_module_example](https://github.com/Nexusoft/minimal_module_example) - A minimal "Hello World" type of module to demonstrate what a simple Nexus Wallet Module needs at minimum.

- [simple_react_module_example](https://github.com/Nexusoft/simple_react_module_example) - A simple example module using React.

- [react-redux_module_example](https://github.com/Nexusoft/react_redux_module_example) - A little more complex module built on React and Redux, showcasing some advanced features that Nexus Wallet Modules provide.

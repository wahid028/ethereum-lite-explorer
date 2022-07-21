# Ethereum Lite Explorer by Alethio
The **Lite Explorer**  is a client-side only web application that connects directly to a [Ethereum JSON RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC) compatible node.
This means you can have your own private Ethereum Explorer should you wish so.
No need for servers, hosting or trusting any third parties to display chain data.

![image](https://user-images.githubusercontent.com/48195837/180142294-b50a80bf-df50-443f-91a7-2695985c26cc.png)


[![CircleCI](https://circleci.com/gh/Alethio/ethereum-lite-explorer.svg?style=svg)](https://circleci.com/gh/Alethio/ethereum-lite-explorer)
[![Docker](https://images.microbadger.com/badges/version/alethio/ethereum-lite-explorer.svg)](https://hub.docker.com/r/alethio/ethereum-lite-explorer "Get Ethereum Lite Explorer through Docker Hub")

> **WARNING v1.x.x is a breaking update from previous v0.x.x releases**

> NOTICE
> Please report any bugs using Github's [issues](https://github.com/Alethio/ethereum-lite-explorer/issues/)

## Contents
<!-- TOC depthFrom:2 depthTo:4 -->

- [Contents](#contents)
- [Technical Details](#technical-details)
    - [Project structure](#project-structure)
- [Getting started](#getting-started)
    - [Prerequisites](#prerequisites)
    - [Configuration](#configuration)
        - [Building from source](#building-from-source)
        - [Deploying the built assets to production](#deploying-the-built-assets-to-production)
        - [Custom build arguments](#custom-build-arguments)

## Technical Details

The project is built on a React/MobX and TypeScript stack, using the [Alethio CMS](https://github.com/Alethio/cms), which allows us to add extensions dynamically through 3rd party plugins.
The basic functionality of the explorer is implemented via a series of open-source [core plugins](https://github.com/Alethio/explorer-core-plugins), which we also use internally for our [aleth.io](https://aleth.io) platform. Please refer to [Alethio CMS](https://github.com/Alethio/cms) for documentation on the plugin system.

### Project structure
```
📁ethereum-lite-explorer
├─📁dev             - dev server for serving the app
├─📁dist            - target folder for application that contains deployables
└─📁src             - source files
  ├─📁app (*1)      - application source code
  ├─📁assets        - static assets (e.g. images) that will be bundled together with the application
  └─📁public        - contains static assets that are copied to the dist folder as they are

(*1)
📁app
├─📁components      - React components
├─📁translation     - localized strings
├─📁util            - application-agnostic utilities. Ideally these would be in a separate repo/package.
└─📄index.ts         - entry point
```

## Getting started

### Prerequisites
Please make sure you have the following installed and running properly
- [Node.js](https://nodejs.org/en/download/) >= 8.0 or [Docker](https://www.docker.com/)
- If building it you will also need NPM >= 6.9 (NPM is distributed with Node.js. For more information see: https://www.npmjs.com/get-npm)
- A JSON-RPC enabled and accessible Ethereum Client, some examples:
    * [An Infura Account](#with-infura)
    * [Parity Light Client](#with-parity-light-client)
    * [Ganache](#with-ganache)
    * [Besu Dev Mode](#with-besu) - private chain example
- If not using the pre-built Docker images, you will need an HTTP server for serving the app and it must be deployed at the root of the domain/subdomain.

### Configuration

### Building from source
Clone the explorer in a folder of your choosing
```sh
$ git clone https://github.com/Alethio/ethereum-lite-explorer.git
$ cd ethereum-lite-explorer
```

**IMPORTANT**: Make sure you are using npm 6.9+ for the next step. Older versions will NOT work due to `alias` feature usages introduced in npm 6.9.

Install npm packages
```sh
$ npm install
```

Copy the sample config file
```sh
$ cp config.default.json config.dev.json
```
Make necessary modifications into `config.dev.json` if needed. For development, you must also remove the version query strings `?v=#.#.#` from the `"plugins"` URIs. Full list of configuration options available [here](#configuration). If you want to add your custom network just modify the nodeUrl.

To start the development build run the following command:
```sh
$ npm run watch
```

This terminal will be kept open, as the above command continuously watches the source files for changes and triggers an incremental build on every change.

Alternatively, to build the minified version (used also for `production`) use:
```sh
$ npm run build
```

Since the app is using the Alethio CMS for using the core plugins the next step is to install them:
```sh
$ npm i -g @alethio/cms-plugin-tool
$ acp install --dev \
    @alethio/explorer-plugin-eth-common \
    @alethio/explorer-plugin-eth-lite \
    @alethio/explorer-plugin-eth-memento \
    @alethio/explorer-plugin-3box
```

If you need other custom plugins like for example to decode the extraData field of a block for the IBFT2 based networks, you can install them at this step:
```sh
$ acp install --dev @alethio/explorer-plugin-eth-ibft2
```

The above command `acp` installs the plugins in the `dist` folder. Basically they will be copied, together with the base app.

**IMPORTANT**: Whenever you use `npm run build` or `npm run build-dev` the `dist` folder is emptied, thus the plugins are also deleted and they need to be reinstalled.

Finally, you can start the local Explorer development server with
```sh
$ npm start
```



# Getting started

In most cases the easiest way to get started is to use [the TypeScript templates (opens in a new tab)](https://github.com/latticexyz/mud/tree/main/templates) provided by [Lattice (opens in a new tab)](https://lattice.xyz/).

## Prerequisites[](#prerequisites)

To install the TypeScript templates you need:

- [Node.js v18 (opens in a new tab)](https://nodejs.org/en/download/package-manager). Note that version 18 is _required_. We do not support older or newer versions at the moment.
    
    Installation instructions
    
    Linux and MacOSWindows
    
    ```
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash nvm install 18
    ```
    
- [git (opens in a new tab)](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
    
    Installation instructions
    
    Linux (RedHat based)Linux (Debian based)MacOSWindows
    
    `sh copy sudo dnf install git-all`
    
- [Foundry (opens in a new tab)](https://book.getfoundry.sh/getting-started/installation)
    
    Installation instructions
    
    Linux and MacOSWindows
    
    ```
    curl -L https://foundry.paradigm.xyz | bash export PATH=$PATH:~/.foundry/bin
    ```
    
- [pnpm, at least version 8 (opens in a new tab)](https://pnpm.io/)
    
    Installation instructions
    
    ```
    sudo npm install -g pnpm
    ```
    

## Installation[](#installation)

To run any of the TypeScript templates:

1. Create the template with pnpm. If you need, replace `tutorial` with your application name.
    
    ```
    pnpm create mud tutorial
    ```
    
    Possible errors in this stage
    
    - `ERROR  No package.json (or package.yaml, or package.json5) was found in "/home/qbzzt1"`
        
        `pnpm` version too old. Run `sudo npm install -g pnpm` to update to the latest.
        
    - `ENOENT  ENOENT: no such file or directory, mkdir`
        
        Out of disk space.
        
    - `foundryup: need 'git' (command not found)`
        
        Install [`git` (opens in a new tab)](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
        
    - `SyntaxError: Unexpected token '?'`
        
        `pnpm` version too old. Run `sudo npm install -g pnpm` to update to the latest.
        
    - `tutorial is not empty directory.`
        
        The tutorial needs to be installed in an empty directory. Either delete the directory and its content (`rm -rf tutorial`) or use a different directory.
        
    
2. Select the template:
    
    - [Vanilla](/templates/typescript/vanilla)
    - [React-ECS](/templates/typescript/react-ecs)
    - React
    - Phaser
    - [Three.js](/templates/typescript/threejs)
3. Start the development server, which updates automatically when you modify files.
    
    ```
    cd tutorial
    pnpm dev
    ```
    
    You can use the arrow keys to move between the logs for the `contracts` package and the logs for the `client` package.
    
    You can press **Q** to quit `pnpm dev`. If that doesn't work, press **Ctrl-A** and then **Q**.
    
    Possible errors in this stage
    
    - `error binding to 127.0.0.1:8545: error creating server listener: Address already in use`
        
        [Foundry's `anvil` (opens in a new tab)](https://book.getfoundry.sh/anvil/) is already running, possibly inside a different `pnpm dev`.
        
        On Linux you can use `sudo netstat -anpe | grep 8545 | grep LISTEN` to get the anvil process ID. You can stop the `anvil` process listening on port 8545 using this command.
        
        ```
        kill `sudo netstat -anpe | grep 8545 | grep LISTEN | awk '{print $9}' | sed 's/\/anvil//'`
        ```
        
        Alternatively, a different Ethereum client such as [Geth (opens in a new tab)](https://geth.ethereum.org/) or [Reth (opens in a new tab)](https://paradigmxyz.github.io/reth/intro.html) might be running. As those can be production clients, it is best not to develop on a machine that runs them.
        
    - `[vite] Internal server error: Failed to resolve import "contracts/out/IWorld.sol/IWorld.abi.json" from "src/mud/setupNetwork.ts"`
        
        Ignore it. It simply means the `contracts` package hasn't created the `IWorld.abi.json` file, which the `client` package needs, yet. As soon as it is created, the `client` package will use it.
        
    - `Waiting for localhost:8545......`
        
        This error happens when `anvil` isn't up yet. If it persists, check the `contracts` package.
        
    
    ⓘ
    
    If nothing else works, the problem may be with [`mprocs` (opens in a new tab)](https://github.com/pvolok/mprocs). We can bypass `mprocs` by running two separate command line windows.
    
    1. In the first command line window run these commands to start the client:
        
        ```
        cd packages/client
        pnpm dev
        ```
        
    2. In the second command line window run these commands to compile the contracts and start `anvil`:
        
        ```
        cd packages/contracts
        forge || export PATH=$PATH:~/.foundry/bin;
        pnpm dev
        ```
        
    
4. Browse to [`http://127.0.0.1:3000` (opens in a new tab)](http://127.0.0.1:3000). If port 3000 is occupied then the front-end package, [Vite (opens in a new tab)](https://vitejs.dev/), will try to listen to port 3001, then 3002, etc.
    

## Using the templates[](#using-the-templates)

### Code organization[](#code-organization)

Other than a few utility files, most of the code is in two packages:

- `packages/contracts` contains the onchain part of the application.
- `packages/client` contains the offchain part of the application, the user interface. The app runs in the user's browser, in development it is served from a [vite (opens in a new tab)](https://vitejs.dev/) server. The content of this package varies based on the template used.

### Vanilla and react-ecs[](#vanilla-and-react-ecs)

The applications in these two templates is the same. You have an onchain counter and you can press `Increment` to increment it.

### Three.js[](#threejs)

In this application the position of an object, the big green cube, is stored onchain. In the user interface you can move that cube using the arrow keys, space, and ctrl.

[What is MUD?](/introduction "What is MUD?")[Introduction](/store/introduction "Introduction")

---

MIT 2023 © MUD

Getting started – MUD
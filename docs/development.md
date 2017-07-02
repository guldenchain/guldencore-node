# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop guldencore-node:

```bash
cd ~
git clone git@github.com:<yourusername>/guldencore-node.git
git clone git@github.com:<yourusername>/guldencore-lib.git
```

To develop gulden or to compile from source:

```bash
git clone git@github.com:<yourusername>/gulden.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See gulden documentation for building gulden on your platform.


## Install Development Dependencies

For Ubuntu:
```bash
sudo apt-get install libzmq3-dev
sudo apt-get install build-essential
```
**Note**: Make sure that libzmq-dev is not installed, it should be removed when installing libzmq3-dev.


For Mac OS X:
```bash
brew install zeromq
```

## Install and Symlink

```bash
cd guldencore-lib
npm install
cd ../guldencore-node
npm install
```
**Note**: If you get a message about not being able to download gulden distribution, you'll need to compile guldend from source, and setup your configuration to use that version.


We now will setup symlinks in `guldencore-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf guldencore-lib
ln -s ~/guldencore-lib
rm -rf bitcoind-rpc
ln -s ~/bitcoind-rpc
```

And if you're compiling or developing gulden:
```bash
cd ../bin
ln -sf ~/gulden/src/guldend
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd guldencore-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/bitcoind.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/bitcoind.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch guldencore-node.json
touch package.json
```

Edit `guldencore-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "guldend",
    "web",
    "insight-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "guldend": {
      "spawn": {
        "datadir": "/home/<youruser>/.gulden",
        "exec": "/home/<youruser>/gulden/src/guldend"
      }
    }
  }
}
```

**Note**: To install services [insight-api](https://github.com/bitpay/insight-api) and [insight-ui](https://github.com/bitpay/insight-ui) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/guldencore-lib
ln -s ~/guldencore-node
ln -s ~/insight-api
ln -s ~/insight-ui
```

Make sure that the `<datadir>/gulden.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:29332
zmqpubhashblock=tcp://127.0.0.1:29332
rpcallowip=127.0.0.1
rpcuser=gulden
rpcpassword=local321
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../guldencore-node/bin/guldencore-node start
```

# Things Gateway by Mozilla

[![Build Status](https://travis-ci.org/mozilla-iot/gateway.svg?branch=master)](https://travis-ci.org/mozilla-iot/gateway)
[![codecov](https://codecov.io/gh/mozilla-iot/gateway/branch/master/graph/badge.svg)](https://codecov.io/gh/mozilla-iot/gateway)
[![dependencies](https://david-dm.org/mozilla-iot/gateway.svg)](https://david-dm.org/mozilla-iot/gateway)
[![devdependencies](https://david-dm.org/mozilla-iot/gateway/dev-status.svg)](https://david-dm.org/mozilla-iot/gateway#info=devDependencies)
[![license](https://img.shields.io/badge/license-MPL--2.0-blue.svg)](LICENSE)

Web of Things gateway.

If you have a Rasberry Pi, the easiest way to use the gateway is to [download and flash](http://iot.mozilla.org/gateway/) a pre-built software image from Mozilla to an SD card. Otherwise you can build it from source yourself (see below).

## Prerequisites for Building

### Install OS

(If you're just installing on your PC, you can skip this step).

If you're installing on a Raspberry Pi then you may need to set up the OS on the Raspberry Pi first. [See here](https://github.com/mozilla-iot/wiki/wiki/Setting-up-Raspberry-Pi) for instructions.

### Install pkg-config

Under Linux:
```
$ sudo apt-get install pkg-config
```

Under OSX:

[See here](http://macappstore.org/pkg-config/).


### Install nvm (Recommended)

nvm allows you to easily install different versions of node. To install nvm:

```
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

Close and reopen your terminal window. Use nvm to install a LTS version of node and set the
default version.

```
$ nvm install --lts
$ nvm use --lts
$ nvm alias default lts/*
```

Verify that node and npm have been installed:
```
$ npm --version
5.6.0
$ node --version
v8.9.4
```

Note: these versions might differ from the LTS version installed locally.

### Install node (if you didn't use nvm)

(If you already installed node via nvm you can skip this step)

Follow the directions from [NodeJS](https://nodejs.org) to install on your platform.

### Install libusb and libudev (Linux only)
```
$ sudo apt-get install libusb-1.0-0-dev libudev-dev
```

### Install git

You'll need git to checkout the repositories.

`$ sudo apt-get install git`

### Build and Install openzwave

```
$ cd
$ git clone https://github.com/OpenZWave/open-zwave.git
$ cd open-zwave
$ make && sudo make install
$ sudo ldconfig
```

Note: You may need to manually add `/usr/local/lib` to your `LD_LIBRARY_PATH` enviroment variable by adding the following to your `~/.profile` file:

`export LD_LIBRARY_PATH=/usr/local/lib/:$LD_LIBRARY_PATH`

You can run this on the command line as well so it has immediate effect. After
running it, you should run `sudo ldconfig` again to make sure the configuration
change goes through.

## Download and Build Gateway

Clone the GitHub repository (or fork it first):
```
$ cd
$ git clone https://github.com/mozilla-iot/gateway.git
```

Change into the gateway directory:

```
$ cd gateway
```

Install dependencies:

NOTE: `yarn` is preferred but `npm install` will also work. To install `yarn`
run `npm install --global yarn` or for a more secure installation follow the
[directions for your OS](https://yarnpkg.com/en/docs/install).

```
$ yarn
```

 Add SSL certificate:

 If you don't plan on using Mozilla's provided tunneling service to set up a `*.mozilla-iot.org` domain, you can use your own SSL certificate. The HTTPS server looks for `privatekey.pem` and `certificate.pem` in the `ssl` sub-directory of the `userProfile` directory specified in your config. You can use a real certificate or generate a self-signed one by following the steps below.

 ```
 $ MOZIOT_HOME="${MOZIOT_HOME:=${HOME}/.mozilla-iot}"
 $ SSL_DIR="${MOZIOT_HOME}/ssl"
 $ [ ! -d "${SSL_DIR}" ] && mkdir -p "${SSL_DIR}"
 $ openssl genrsa -out "${SSL_DIR}/privatekey.pem" 2048
 $ openssl req -new -sha256 -key "${SSL_DIR}/privatekey.pem" -out "${SSL_DIR}/csr.pem"
 $ openssl x509 -req -in "${SSL_DIR}/csr.pem" -signkey "${SSL_DIR}/privatekey.pem" -out "${SSL_DIR}/certificate.pem"
```

 Start the web server:

```
$ npm start
```

Load ```https://localhost:4443``` in your web browser (or use the server's IP address if loading remotely).
Since you're using a self-signed certificate, you'll need to add a security exception in the browser.

## Debugging

If you are using VS Code, simply use the "launch" target. It will build the gateway in debugger mode.

If you are not using VS Code, run `npm run debug` and it will build the gateway and launch it with `--inspect`.

## Running Tests
To run the linter and all tests:
```
$ npm test
```

To run a single test:
```
$ jest src/test/{test-name}.js
```
(assumes you have the ```jest``` command on your ```PATH```, otherwise use ```./node_modules/jest/bin/jest.js```)

## Source Code Structure

* **`config/`** - Gateway configuration files
* **`doc/`** - Additional documentation
* **`image/`** - Tools for building the Raspberry Pi image
* **`src/`**
  * **`addons/`** - Utility classes and methods used by add-ons
  * **`addons-test/`** - Add-ons used strictly for testing
  * **`controllers/`** - App URL routes and their logic
  * **`models/`** - Data model and business logic
  * **`rules-engine/`** - The rules engine
  * **`test/`** - Integration tests
  * **`views/`** - HTML views
  * **`addon-loader.js`** - Script used for starting up Node-based add-ons
  * **`addon-manager.js`** - Manages add-ons (e.g. ZigBee, Z-Wave)
  * **`app.js`** - The main back end
  * **`app-instance.js`** - Application wrapper for integration tests
  * **`constants.js`** - System-wide constants
  * **`db.js`** - Manages the SQLite3 database
  * **`ec-crypto.js`** - Elliptic curve helpers for the ES256 curve
  * **`jwt-middleware.js`** - Express middleware for determining authentication status
  * **`log-timestamps.js`** - Utilities for adding timestamps to console logging functions
  * **`oauth-types.js`** - OAuth types
  * **`passwords.js`** - Password utilities
  * **`router.js`** - Routes app URLs to controllers
  * **`ssltunnel.js`** - Utilities to determine state of tunnel and manage the PageKite process
  * **`tunnel_setup.js`** - Express middleware to determine if the tunnel is set up
  * **`user-profile.js`** - Manages persistent user data
* **`static/`** - Static CSS, JavaScript & image resources for web app front end
* **`tools/`** - Helpful utilities (not part of the build)
* **`package.json`** - npm module manifest

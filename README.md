# KIN app for the Ledger Nano S and Ledger Blue

## Introduction

This is the wallet app for the [Ledger Nano S](https://www.ledgerwallet.com/products/ledger-nano-s) and [Ledger Blue](https://www.ledgerwallet.com/products/ledger-blue) that makes it possible to store [KIN](https://kinecosystem.org/) coin on those devices and sign payment transactions for the KIN network.

A companion [Javascript library](https://github.com/LedgerHQ/ledgerjs) is available to communicate with this app. To learn how to use this library and generate a browserified version of it you can take look at the [demo project](https://github.com/lenondupe/ledgerjs-stellar).

## Building and installing

To build and install the app on your Nano S or Blue you must set up the Ledger Nano S or Blue build environments. Please follow the Getting Started instructions at the [Ledger Nano S github repository](https://github.com/LedgerHQ/ledger-nano-s).

Alternatively, you can set up the Vagrant Virtualbox Ledger environment maintained [here](https://github.com/fix/ledger-vagrant). This sets up an Ubuntu virtual machine with the Ledger build environment already set up.

Use the following command to build the app:

```$ make BOLOS_ENV=/opt/ledger-blue/ BOLOS_SDK=/home/dev/nanos-secure-sdk```

The command to load the app onto the device:

```$ make load BOLOS_SDK=/opt/nanos-secure-sdk```

To remove the app from the device do:

```$ make delete BOLOS_SDK=/opt/nanos-secure-sdk```

**Do not forget to provide the correct locations of `BOLOS_ENV` and `BOLOS_SDK`.**

## Testing

The `./test` directory contains files for testing the xdr transaction parser and the screen formatter. To build and execute the tests run `./test.sh`.

### XDR parsing

When a transaction is to be signed it is sent to the device as an [XDR](https://tools.ietf.org/html/rfc1832) serialized binary object. To show the transaction details to the user on the device this binary object must be read. This is done by a purpose-built parser shipped with this app.

Due to memory limitations the transaction maximum size is set to 1kb. This should be sufficient for most usages, including multi-operation transactions up to 15 operations depending on the size of the operations.

Alternatively the user can enable hash signing. In this mode the transaction XDR is not sent to the device but only the hash of the transaction, which is the basis for a valid signature. In this case details for the transaction cannot be displayed and verified which is why this is not the preferred mode of operation. In fact, setting hash signing mode is not persistent and needs be set again whenever the user needs it.
 
## Key pair validation

The operation to retrieve the public key implements an optional keypair verification method. Along with the request to retrieve the public key a small message is sent that is to be signed by the device. Back on the host the returned signature can be checked against the returned public key. This is to guard against incompatibility between the keypairs generated by the Ledger device and the ones expected by the Stellar network, whatever the reason for this might be. The extra precaution prevents users from sending funds to an address they are not able to sign transactions for.

## Standalone application loader

The loading command above requires a correct setup of the Python development environment. If you want someone to be able to install the app without 
any previous setup, you can create a standalone fully-contained loader, that embeds Python interpreter, loader files and application files. 
For this purpose, a standalone loader script (loader.py)[loader.py] and a (PyInstaller)[https://pypi.org/project/PyInstaller/] spec (loader.spec)[loader.spec] 
are created.

1. Install PyInstaller:

```$ pip install pyinstaller```

2. Check that the standalone loader script works for you:

```$ python loader.py```

3. Build the stanalone loader application:

```$ pyinstaller loader.spec```

4. Make it executable:

```$ chmod +x dist/loader```

5. Check it:

```$ ./dist/loader```

Now you can send the executable to anyone who is willing to test your app. 
**WARNING**: for preproduction use only! Make sure to reinstall the app when it is official in Ledger Live!

# Getting Started with monero-javascript Part 1: Installation and Basic Usage

## What is monero-javascript?

Monero-javascript is a Node.js-based Monero library for javascript.

The library derives its object and method hierarchy from [The Hidden Model](https://moneroecosystem.org/monero-java/monero-spec.pdf), a concise, uniform, and intuitive API specification that also serves as the foundation for the [monero-cpp](https://github.com/woodser/monero-cpp-library) and [monero-java](https://github.com/monero-ecosystem/monero-java) libraries.

In addition to traditional RPC wallet communication, monero-javascript can manage wallets natively with WebAssembly (Wasm). Monero-javascript's Wasm wallet lets developers perform completely trustless, client-side wallet operations by eliminating the need for an intermediary wallet RPC server.

![Monero-javascript hierarchy](../img/architecture.png?raw=true)*Monero-javascript manipulates XMR and communicates with the blockchain via three channels: RPC wallet servers, RPC daemon servers, and Wasm wallets.*  

## Initial Setup

### Install Node.js and the Node package manager (npm)

Make sure you have Node.js and npm installed; you need them to obtain and use the monero-javascript library. See the ["Node.js and npm"](https://github.com/monero-ecosystem/monero-javascript/blob/master/docs/developer_guide/installing_prerequisite_software.md#nodejs-and-npm) section of the prerequisite installation guide for step-by-step instructions for downloading and installing Node.js and npm.

### Create a new Node.js project

1. Create and enter a new directory to hold the project:
  ```
  mkdir ~/offline_wallet_generator
  cd ~/offline_wallet_generator
  ```
2. Create the new project:
  `npm init -y`
3. Add the monero-javascript library to the offline_wallet_generator package:
  `npm install --save monero-javascript`

## Write a monero-javascript program

### The offline wallet generator

An offline wallet generator creates and displays a new, random wallet address along with that address's associated view key, spend key, and mnemonic seed phrase. An offline wallet generator only needs to generate and display these wallet attributes; it does not need to communicate with a Monero network, transfer XMR, or track a wallet's balance or outputs.

Monero-javascript's keys-only wallet more than meets these requirements and is the ideal basis for a monero-javascript-based offline wallet generator. The monero-javascript wallet provides a minimal Wasm wallet implementation, called a keys-only wllet, that tracks only the wallet's _permanent_ attributes. It can not initiate transfers, report its balance, or communicate with a Monero network. The trade off for these limitations is a small file size - just under 1/5 that of a standard Wasm wallet. The smaller memory footprint allows programs to load more quickly an run more efficiently. Therefore, you should use keys-only wallets whenever your program does not require a full Wasm or RPC wallet.

### Write the essential monero-javascript code

Open your preferred text editor or IDE and copy the following code to a new, blank file:

```
require("monero-javascript");

mainFunction();

async mainFunction() {
}
```

Note the program's two components:
1. A "require" statement to import the monero-javascript library:
`require("monero-javascript");`
2. An asynchronous "main" function so that "await" statements can precede calls to asynchronous monero-javascript methods:
`async mainFunction() {}`
The asynchronous function is not strictly necessary, but most applications need to call monero-javascript methods from an ayschnronous function so that code execution can pause while monero-javascript methods run. Otherwise, the app may try to manage a wallet or query an RPC server connection before loading completely.

### Building a keys-only wallet

Monero-javscript implements keys-only wallets in the MoneroWalletKeys class. You can create a random keys-only wallet by calling the [MoneroWalletKeys](moneroecosystem.org/monero-javascript/MoneroWalletKeys.html) class's `createWallet()` method in mainFunction() as follows:
```
// create a random keys-only (offline) stagenet wallet
var keysOnlyWallet = await MoneroWalletKeys.createWallet({networkType: MoneroNetworkType.STAGENET, language: "English"});
```

The createWallet method accepts a [MoneroWalletConfig](moneroecosystem.org/monero-javascript/MoneroWalletConfig) argument. MoneroWalletConfig is a generic class for passing wallet attributes to any monero-javascript wallet creation method. Each wallet constructor can determine how to create the new wallet by evaluating which MoneroWalletConfig fields are present and which are absent. If the MoneroWalletConfig object passed to createWallet() does not specify any identifying attributes (such as an address or seed phrase), the wallet class will generate a random wallet address.

Use the Wallet class's getter methods to obtain and log the wallet's basic attributes. These attributes are:
* The seed phrase
* The address
* The spend key
* The view key

Get each wallet attribute and log it in the console.
```
console.log("Seed phrase: " + await(walletKeys.getMnemonic()));
console.log("Address: " + await(walletKeys.getAddress(0,0))); // MoneroWallet.getAddress(accountIndex, subAddress)
console.log("Spend key: " + await(walletKeys.getPrivateSpendKey()));
console.log("View key: " + await(walletKeys.getPrivateViewKey()));
```

The finished program should match the following:

```
require("monero-javascript");

await mainFunction();

async function mainFunction() {
  // create a random keys-only (offline) stagenet wallet
  var walletKeys = await MoneroWalletKeys.createWallet({networkType: MoneroNetworkType.STAGENET, language: "English"});

  console.log("Seed phrase: " + await(walletKeys.getMnemonic()));
  console.log("Address: " + await(walletKeys.getAddress(0,0))); // MoneroWallet.getAddress(accountIndex, subAddress)
  console.log("Spend key: " + await(walletKeys.getPrivateSpendKey()));
  console.log("View key: " + await(walletKeys.getPrivateViewKey()));
}
```
Save the file as "offline_wallet_generator.js" and run the program with Node.jsw:

`node offline_wallet_generator.js`

The output should look similar to the following:
```
Seed phrase: darted oatmeal toenail launching frown empty agenda apply unnoticed blip waist ashtray threaten deftly sawmill rotate skirting origin ahead obtains makeup bakery bounced dagger apply
Address: 5ATdKTGQpETCHbBHgDhwd1Wi7oo52PVZYjk2ucf5fnkn9T5yKau2UXkbm7Mo23SAx4MRdyvAaVq75LY9EjSPQnorCGebFqg
Spend key: 7bf64c44ecb5ecf02261e6d721d6201d138d0891f0fcf4d613dc27ec84bc070e
View key: b4e167b76888bf6ad4c1ab23b4d1bb2e57e7c082ac96478bcda4a9af7fd19507
```

## The next step

Continue to [Getting Started with Monero Javascript Part 2: Creating a Web Application](https://github.com/monero-ecosystem/monero-javascript/blob/master/docs/developer_guide/web_app_guide.md) to learn how to write client-side web browser applications with monero-javascript.

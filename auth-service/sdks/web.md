---
description: >-
  This documentation for the Particle Network Javascript SDK will get you
  started in minutes!
---

# Web

Particle Network SDK allows you to easily integrate your app with the EVM blockchain, whether you already have a dApp integrated with web3 or are starting from scratch. Particle Network provides a smooth and delightful experience for both you and your app's users.

## Get Started

### Step 1: Include Particle Network SDK Script

Download Particle Network SDK to your project via NPM

```bash
npm i --save @particle-network/provider
```

### Step 2: Setup Developer API Key

Before you can add Auth Service to your app, you need to create a Particle project to connect to your app. Visit [Particle Dashboard](broken-reference) to learn more about Particle projects and apps.

[👉 Sign up/log in and create your project now](https://particle.network/#/login)

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({
  projectId: "xx",
  clientKey: "xx",
  appId: "xx",
  chainName: "ethereum",
  chainId: 42,
});

window.web3 = new Web3(pn.getProvider());
window.web3.currentProvider.isParticleNetwork // => true
```

Your first Particle Network dApp! 🎉 You can implement web3 functionalities just like how you would normally with MetaMask.

## Web3 Integration

### Network Configuration

```typescript
import { ParticleNetwork } from "@particle-network/provider";

//ethereum Kovan test net
const pn = new ParticleNetwork({
  projectId: "xx",
  clientKey: "xx",
  appId: "xx",
  chainName: "ethereum",
  chainId: 42, //mainnet 1
});

//bsc testnet
const pn = new ParticleNetwork({
  projectId: "xx",
  clientKey: "xx",
  appId: "xx",
  chainName: "bsc",
  chainId: 97, // mainnet 56
});

//...
```

Particle Network support EVM blockchain.

* ethereum
  * 1
  * 42
* bsc
  * 56
  * 97
* avalanche
  * 43114
  * 43113
* ploygon
  * 137
  * 80001
* fantom
  * 250
  * 4002
* arbitrum
  * 42161
  * 421611
* moonbeam
  * 1284
  * 1287
* moonriver
  * 1285
  * 1287
* heco
  * 128
  * 256
* aurora
  * 1313161554
  * 1313161555
* harmony
  * 1666600000
  * 1666700000

### Get User Account

In order for web3 to work and grab the end-users' Ethereum wallet addresses, the users have to login first (similar to unlocking account in MetaMask). You can simply trigger the login for users with the web3 function call below.

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

// Request user login if needed, returns current user account address
web3.currentProvider.enable();
```

User login modal can also be triggered through web3 accounts and coinbase functions.

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

// Async functions that triggers login modal, if user not already logged in
web3.eth.getAccounts((error, accounts) => {
  if (error) throw error;
  console.log(accounts); // ['0x...']
});
web3.eth.getCoinbase((error, coinbase) => {
  if (error) throw error;
  console.log(coinbase); // '0x...'
});
```

A modal will open to ask users to sign up for an account or login with their mobile phone number or email.

### Send Transaction

If you have replaced your web3 provider with Particle Network provider, nothing needs to be changed for web3 send Ether transactions to continue working.

The Particle Network X modal will pop open and ask users to confirm their transaction once this web3 function is called on the client-side.

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

const txnParams = {
    from: "0xXX",
    to: toAddress,
    value: sendValue
};
window.web3.eth.sendTransaction(txnParams, (error, txnHash) => {
    if (error) throw error;
    console.log(txnHash);
});
```

### User Signing

This is a relatively advanced use case. If you use the signed typed data JSONRPC endpoint, Particle Network will support this as well.

{% tabs %}
{% tab title="Personal Sign" %}
```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";
import { Buffer } from "buffer";
import { bufferToHex, toChecksumAddress } from "ethereumjs-util";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

const text = "Hello Particle Network!";
const accounts = await window.web3.eth.getAccounts();
const from = accounts[0];
const msg = bufferToHex(Buffer.from(text, "utf8"));
const params = [msg, from];
const method = "personal_sign";

window.web3.currentProvider
  .request({
    method,
    params,
    from,
  })
  .then((result) => {
    console.log("personal_sign", result);
  })
  .catch((error) => {
    console.error("personal_sign", error);
  });
```
{% endtab %}

{% tab title="Sign Typed Data v1" %}
```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());


const accounts = await window.web3.eth.getAccounts();
const from = accounts[0];
const msg = [
    {
      type: "string",
      name: "fullName",
      value: "John Doe",
    },
    {
      type: "uint32",
      name: "userId",
      value: "1234",
    },
  ];
const params = [msg, from];
const method = "eth_signTypedData_v1";

window.web3.currentProvider
  .request({
    method,
    params,
    from,
  })
  .then((result) => {
    console.log("signTypedData result", result);
  })
  .catch((err) => {
    console.log("signTypedData error", err);
  });
```
{% endtab %}

{% tab title="Sign Typed Data v3" %}
```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

const accounts = await window.web3.eth.getAccounts();
const from = accounts[0];
const payload = {
    types: {
      EIP712Domain: [
        { name: "name", type: "string" },
        { name: "version", type: "string" },
        { name: "verifyingContract", type: "address" },
      ],
      Person: [
        { name: "name", type: "string" },
        { name: "wallet", type: "address" },
      ],
      Mail: [
        { name: "from", type: "Person" },
        { name: "to", type: "Person" },
        { name: "contents", type: "string" },
      ],
    },
    primaryType: "Mail",
    domain: {
      name: "Ether Mail",
      version: "1",
      verifyingContract: "0xCcCCccccCCCCcCCCCCCcCcCccCcCCCcCcccccccC",
    },
    message: {
      from: {
        name: "Cow",
        wallet: "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826",
      },
      to: {
        name: "Bob",
        wallet: "0xbBbBBBBbbBBBbbbBbbBbbbbBBbBbbbbBbBbbBBbB",
      },
      contents: "Hello, Bob!",
    },
  };
const params = [from, payload];
const method = "eth_signTypedData_v3";

window.web3.currentProvider
  .request({
    method,
    params,
    from,
  })
  .then((result) => {
    console.log("signTypedData_v3 result", result);
  })
  .catch((err) => {
    console.log("signTypedData_v3 error", err);
  });
```
{% endtab %}

{% tab title="Sign Typed Data v4" %}
```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

const accounts = await window.web3.eth.getAccounts();
const from = accounts[0];
const payload = {
    domain: {
      name: "Ether Mail",
      verifyingContract: "0xCcCCccccCCCCcCCCCCCcCcCccCcCCCcCcccccccC",
      version: "1",
    },
    message: {
      contents: "Hello, Bob!",
      from: {
        name: "Cow",
        wallets: [
          "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826",
          "0xDeaDbeefdEAdbeefdEadbEEFdeadbeEFdEaDbeeF",
        ],
      },
      to: [
        {
          name: "Bob",
          wallets: [
            "0xbBbBBBBbbBBBbbbBbbBbbbbBBbBbbbbBbBbbBBbB",
            "0xB0BdaBea57B0BDABeA57b0bdABEA57b0BDabEa57",
            "0xB0B0b0b0b0b0B000000000000000000000000000",
          ],
        },
      ],
    },
    primaryType: "Mail",
    types: {
      EIP712Domain: [
        { name: "name", type: "string" },
        { name: "version", type: "string" },
        { name: "verifyingContract", type: "address" },
      ],
      Group: [
        { name: "name", type: "string" },
        { name: "members", type: "Person[]" },
      ],
      Mail: [
        { name: "from", type: "Person" },
        { name: "to", type: "Person[]" },
        { name: "contents", type: "string" },
      ],
      Person: [
        { name: "name", type: "string" },
        { name: "wallets", type: "address[]" },
      ],
    },
  };
const params = [from, payload];
const method = "eth_signTypedData_v4";

window.web3.currentProvider
  .request({
    method,
    params,
    from,
  })
  .then((result) => {
    console.log("signTypedData_v4 result", result);
  })
  .catch((err) => {
    console.log("signTypedData_v4 error", err);
  });
```
{% endtab %}
{% endtabs %}

### Particle Network Native

#### Login

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

pn.auth.login().then(accounts => {
    console.log("login", accounts[0]);
})
```

#### Logout

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

pn.auth.logout().then(() => {
    console.log("logout");
})
```

#### Is User Logged In

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

//check user logged
pn.auth.isLogin()
```

#### Get User Info

```typescript
import { ParticleNetwork } from "@particle-network/provider";
import Web3 from "web3";

const pn = new ParticleNetwork({...});
window.web3 = new Web3(pn.getProvider());

//get user info(token/address/uuid)
const info = pn.auth.userInfo();
```

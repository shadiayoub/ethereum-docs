---
title: Sending Tokens Using ethers.js
author: Kim YongJun
tags:
  - ETHERS.JS
  - ERC-20
  - TOKENS
skill: beginner
lang: en
published: 2021-04-06T00:00:00.000Z
description: Beginner friendly guide to sending tokens using ethers.js.
---

# Sending Tokens Using ethers.js

### Send Token Using ethers.js(5.0) <a href="#send-token" id="send-token"></a>

#### In This Tutorial You'll Learn How To <a href="#you-learn-about" id="you-learn-about"></a>

* Import ethers.js
* Transfer token
* Set gas price according to the network traffic situation

#### To-Get-Started <a href="#to-get-started" id="to-get-started"></a>

To get started, we must first import the ethers.js library into our javascript Include ethers.js(5.0)

#### Installing <a href="#install-ethersjs" id="install-ethersjs"></a>

```shell
/home/ricmoo> npm install --save ethers
```

ES6 in the Browser

```html
<script type="module">
  import { ethers } from "https://cdn.ethers.io/lib/ethers-5.0.esm.min.js"
  // Your code here...
</script>
```

ES3(UMD) in the Browser

```html
<script
  src="https://cdn.ethers.io/lib/ethers-5.0.umd.min.js"
  type="application/javascript"
></script>
```

#### Parameters <a href="#param" id="param"></a>

1. **`contract_address`**: Token contract address (contract address is needed when the token you want to transfer is not ether)
2. **`send_token_amount`**: The amount you want to send to the receiver
3. **`to_address`**: The receiver's address
4. **`send_account`**: The sender's address
5. **`private_key`**: Private key of the sender to sign the transaction and actually transfer the tokens

### Notice <a href="#notice" id="notice"></a>

`signTransaction(tx)` is removed because `sendTransaction()` does it internally.

### Sending Procedures <a href="#procedure" id="procedure"></a>

#### 1. Connect to network (testnet) <a href="#connect-to-network" id="connect-to-network"></a>

**Set Provider (Infura)**

Connect to Ropsten testnet

```javascript
window.ethersProvider = new ethers.providers.InfuraProvider("ropsten")
```

#### 2. Create wallet <a href="#create-wallet" id="create-wallet"></a>

```javascript
let wallet = new ethers.Wallet(private_key)
```

#### 3. Connect Wallet to net <a href="#connect-wallet-to-net" id="connect-wallet-to-net"></a>

```javascript
let walletSigner = wallet.connect(window.ethersProvider)
```

#### 4. Get current gas price <a href="#get-gas" id="get-gas"></a>

```javascript
window.ethersProvider.getGasPrice() // gasPrice
```

#### 5. Define Transaction <a href="#define-transaction" id="define-transaction"></a>

These variables defined below are dependent on `send_token()`

#### Transaction parameters <a href="#transaction-params" id="transaction-params"></a>

1. **`send_account`**: address of the token sender
2. **`to_address`**: address of the token receiver
3. **`send_token_amount`**: the amount of tokens to send
4. **`gas_limit`**: gas limit
5. **`gas_price`**: gas price

[See below for how to use](index.md#how-to-use)

```javascript
const tx = {
  from: send_account,
  to: to_address,
  value: ethers.utils.parseEther(send_token_amount),
  nonce: window.ethersProvider.getTransactionCount(send_account, "latest"),
  gasLimit: ethers.utils.hexlify(gas_limit), // 100000
  gasPrice: gas_price,
}
```

#### 6. Transfer <a href="#transfer" id="transfer"></a>

```javascript
walletSigner.sendTransaction(tx).then((transaction) => {
  console.dir(transaction)
  alert("Send finished!")
})
```

### How to use it <a href="#how-to-use" id="how-to-use"></a>

```javascript
let private_key =
  "41559d28e936dc92104ff30691519693fc753ffbee6251a611b9aa1878f12a4d"
let send_token_amount = "1"
let to_address = "0x4c10D2734Fb76D3236E522509181CC3Ba8DE0e80"
let send_address = "0xda27a282B5B6c5229699891CfA6b900A716539E6"
let gas_limit = "0x100000"
let wallet = new ethers.Wallet(private_key)
let walletSigner = wallet.connect(window.ethersProvider)
let contract_address = ""
window.ethersProvider = new ethers.providers.InfuraProvider("ropsten")

send_token(
  contract_address,
  send_token_amount,
  to_address,
  send_address,
  private_key
)
```

#### Success! <a href="#success" id="success"></a>

![image of transaction done successfully](../../../public/content/developers/tutorials/send-token-ethersjs/successful-transaction.png)

### send\_token() <a href="#send-token-method" id="send-token-method"></a>

```javascript
function send_token(
  contract_address,
  send_token_amount,
  to_address,
  send_account,
  private_key
) {
  let wallet = new ethers.Wallet(private_key)
  let walletSigner = wallet.connect(window.ethersProvider)

  window.ethersProvider.getGasPrice().then((currentGasPrice) => {
    let gas_price = ethers.utils.hexlify(parseInt(currentGasPrice))
    console.log(`gas_price: ${gas_price}`)

    if (contract_address) {
      // general token send
      let contract = new ethers.Contract(
        contract_address,
        send_abi,
        walletSigner
      )

      // How many tokens?
      let numberOfTokens = ethers.utils.parseUnits(send_token_amount, 18)
      console.log(`numberOfTokens: ${numberOfTokens}`)

      // Send tokens
      contract.transfer(to_address, numberOfTokens).then((transferResult) => {
        console.dir(transferResult)
        alert("sent token")
      })
    } // ether send
    else {
      const tx = {
        from: send_account,
        to: to_address,
        value: ethers.utils.parseEther(send_token_amount),
        nonce: window.ethersProvider.getTransactionCount(
          send_account,
          "latest"
        ),
        gasLimit: ethers.utils.hexlify(gas_limit), // 100000
        gasPrice: gas_price,
      }
      console.dir(tx)
      try {
        walletSigner.sendTransaction(tx).then((transaction) => {
          console.dir(transaction)
          alert("Send finished!")
        })
      } catch (error) {
        alert("failed to send!!")
      }
    }
  })
}
```

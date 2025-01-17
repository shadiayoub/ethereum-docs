---
title: Deploying smart contracts
lang: en
---

# Deploying smart contracts

You need to deploy your smart contract for it to be available to users of an Ethereum network.

To deploy a smart contract, you merely send an Ethereum transaction containing the compiled code of the smart contract without specifying any recipient.

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

You should understand [Ethereum networks](../../networks/), [transactions](../../transactions/) and the [anatomy of smart contracts](../anatomy/) before deploying smart contracts.

Deploying a contract also costs ether (ETH) since they are stored on the blockchain, so you should be familiar with [gas and fees](../../gas/) on Ethereum.

Finally, you'll need to compile your contract before deploying it, so make sure you've read about [compiling smart contracts](../compiling/).

### How to deploy a smart contract <a href="#how-to-deploy-a-smart-contract" id="how-to-deploy-a-smart-contract"></a>

#### What you'll need <a href="#what-youll-need" id="what-youll-need"></a>

* Your contract's bytecode – this is generated through [compilation](../compiling/)
* ETH for gas – you'll set your gas limit like other transactions so be aware that contract deployment needs a lot more gas than a simple ETH transfer
* a deployment script or plugin
* access to an [Ethereum node](../../nodes-and-clients/), either by running your own, connecting to a public node, or via an API key using a [node service](../../nodes-and-clients/nodes-as-a-service/)

#### Steps to deploy a smart contract <a href="#steps-to-deploy" id="steps-to-deploy"></a>

The specific steps involved will depend on the development framework in question. For example, you can check out [Hardhat's documentation on deploying your contracts](https://hardhat.org/guides/deploying.html) or [Foundry's documentation on deploying and verifying a smart contract](https://book.getfoundry.sh/forge/deploying). Once deployed, your contract will have an Ethereum address like other [accounts](../../accounts/) and can be verified using [source code verification tools](../verifying/#source-code-verification-tools).

### Related tools <a href="#related-tools" id="related-tools"></a>

**Remix - **_**Remix IDE allows developing, deploying and administering smart contracts for Ethereum like blockchains**_

* [Remix](https://remix.ethereum.org)

**Tenderly - **_**Web3 development platform that provides debugging, observability, and infrastructure building blocks for developing, testing, monitoring, and operating smart contracts**_

* [tenderly.co](https://tenderly.co/)
* [Docs](https://docs.tenderly.co/)
* [GitHub](https://github.com/Tenderly)
* [Discord](https://discord.gg/eCWjuvt)

**Hardhat - **_**A development environment to compile, deploy, test, and debug your Ethereum software**_

* [hardhat.org](https://hardhat.org/getting-started/)
* [Docs on deploying your contracts](https://hardhat.org/guides/deploying.html)
* [GitHub](https://github.com/nomiclabs/hardhat)
* [Discord](https://discord.com/invite/TETZs2KK4k)

**thirdweb - **_**Easily deploy any contract to any EVM compatible chain, using a single command**_

* [Documentation](https://portal.thirdweb.com/deploy/)

**Crossmint - **_**Enterprise-grade web3 development platform to deploy smart contracts, enable credit-card and cross chain payments, and use APIs to create, distribute, sell, store, and edit NFTs.**_

* [crossmint.com](https://www.crossmint.com)
* [Documentation](https://docs.crossmint.com)
* [Discord](https://discord.com/invite/crossmint)
* [Blog](https://blog.crossmint.com)

### Related tutorials <a href="#related-tutorials" id="related-tutorials"></a>

* [Deploying your first smart contract](../../../tutorials/deploying-your-first-smart-contract/) _– An introduction to deploying your first smart contract on an Ethereum test network._
* [Hello World | smart contract tutorial](../../../tutorials/hello-world-smart-contract/) _– An easy-to-follow tutorial to create & deploy a basic smart contract on Ethereum._
* [Interact with other contracts from Solidity](../../../tutorials/interact-with-other-contracts-from-solidity/) _– How to deploy a smart contract from an existing contract and interact with it._
* [How to downsize your contract size](../../../tutorials/downsizing-contracts-to-fight-the-contract-size-limit/) _- How to reduce your contract's size to keep it under the limit and save on gas_

### Further reading <a href="#further-reading" id="further-reading"></a>

* [https://docs.openzeppelin.com/learn/deploying-and-interacting](https://docs.openzeppelin.com/learn/deploying-and-interacting) - _OpenZeppelin_
* [Deploying your contracts with Hardhat](https://hardhat.org/guides/deploying.html) - _Nomic Labs_

_Know of a community resource that helped you? Edit this page and add it!_

### Related topics <a href="#related-topics" id="related-topics"></a>

* [Development frameworks](../../frameworks/)
* [Run an Ethereum node](../../nodes-and-clients/run-a-node/)
* [Nodes-as-a-service](../../nodes-and-clients/nodes-as-a-service/)

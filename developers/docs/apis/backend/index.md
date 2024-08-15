---
title: Backend API libraries
lang: en
description: >-
  An introduction to the Ethereum client APIs that let you interact with the
  blockchain from your application.
---

# Backend API libraries

In order for a software application to interact with the Ethereum blockchain (i.e. read blockchain data and/or send transactions to the network), it must connect to an Ethereum node.

For this purpose, every Ethereum client implements the [JSON-RPC](../json-rpc/) specification, so there is a uniform set of [methods](../json-rpc/#json-rpc-methods) that applications can rely on.

If you want to use a specific programming language to connect with an Ethereum node, there are many convenience libraries within the ecosystem that make this much easier. With these libraries, developers can write intuitive, one-line methods to initialize JSON-RPC requests (under the hood) that interact with Ethereum.

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

It might be helpful to understand the [Ethereum stack](../../ethereum-stack/) and [Ethereum clients](../../nodes-and-clients/).

### Why use a library? <a href="#why-use-a-library" id="why-use-a-library"></a>

These libraries abstract away much of the complexity of interacting directly with an Ethereum node. They also provide utility functions (e.g. converting ETH to Gwei) so as a developer you can spend less time dealing with the intricacies of Ethereum clients and more time focused on the unique functionality of your application.

### Available libraries <a href="#available-libraries" id="available-libraries"></a>

#### Infrastructure and node services <a href="#infrastructure-and-node-services" id="infrastructure-and-node-services"></a>

**Alchemy -** _**Ethereum Development Platform.**_

* [alchemy.com](https://www.alchemy.com/)
* [Documentation](https://docs.alchemy.com/)
* [GitHub](https://github.com/alchemyplatform)
* [Discord](https://discord.com/invite/alchemyplatform)

**All That Node -** _**Node-as-a-Service.**_

* [All That Node.com](https://www.allthatnode.com/)
* [Documentation](https://docs.allthatnode.com)
* [Discord](https://discord.gg/GmcdVEUbJM)

**Blast by Bware Labs -** _**Decentralized APIs for Ethereum Mainnet and Testnets.**_

* [blastapi.io](https://blastapi.io/)
* [Documentation](https://docs.blastapi.io)
* [Discord](https://discord.gg/bwarelabs)

**BlockPi -** _**Provide more efficient and fast RPC services**_

* [blockpi.io](https://blockpi.io/)
* [Documentation](https://docs.blockpi.io/)
* [GitHub](https://github.com/BlockPILabs)
* [Discord](https://discord.com/invite/xTvGVrGVZv)

**Cloudflare Ethereum Gateway.**

* [cloudflare-eth.com](https://www.cloudflare.com/application-services/products/web3/)

**Etherscan - Block Explorer and Transaction APIs**

* [Documentation](https://docs.etherscan.io/)

**GetBlock-** _**Blockchain-as-a-service for Web3 development**_

* [GetBlock.io](https://getblock.io/)
* [Documentation](https://getblock.io/docs/)

**Infura -** _**The Ethereum API as a service.**_

* [infura.io](https://infura.io)
* [Documentation](https://docs.infura.io/api)
* [GitHub](https://github.com/INFURA)

**Node RPC - **_**Cost-effective EVM JSON-RPC provider**_

* [noderpc.xyz](https://www.noderpc.xyz/)
* [Documentation](https://docs.noderpc.xyz/node-rpc)

**NOWNodes - **_**Full Nodes and Block Explorers.**_

* [NOWNodes.io](https://nownodes.io/)
* [Documentation](https://documenter.getpostman.com/view/13630829/TVmFkLwy#intro)

**QuickNode -** _**Blockchain Infrastructure as a Service.**_

* [quicknode.com](https://quicknode.com)
* [Documentation](https://www.quicknode.com/docs/welcome)
* [Discord](https://discord.gg/quicknode)

**Rivet -** _**Ethereum and Ethereum Classic APIs as a service powered by open source software.**_

* [rivet.cloud](https://rivet.cloud)
* [Documentation](https://rivet.cloud/docs/)
* [GitHub](https://github.com/openrelayxyz/ethercattle-deployment)

**Zmok -** _**Speed-oriented Ethereum nodes as JSON-RPC/WebSockets API.**_

* [zmok.io](https://zmok.io/)
* [GitHub](https://github.com/zmok-io)
* [Documentation](https://docs.zmok.io/)
* [Discord](https://discord.gg/fAHeh3ka6s)

#### Development tools <a href="#development-tools" id="development-tools"></a>

**ethers-kt -** _**Async, high-performance Kotlin/Java/Android library for EVM-based blockchains.**_

* [GitHub](https://github.com/Kr1ptal/ethers-kt)
* [Examples](https://github.com/Kr1ptal/ethers-kt/tree/master/examples)
* [Discord](https://discord.gg/rx35NzQGSb)

**Nethereum -** _**An open source .NET integration library for blockchain.**_

* [GitHub](https://github.com/Nethereum/Nethereum)
* [Documentation](http://docs.nethereum.com/en/latest/)
* [Discord](https://discord.com/invite/jQPrR58FxX)

**Python Tooling -** _**Variety of libraries for Ethereum interaction via Python.**_

* [py.ethereum.org](https://python.ethereum.org/)
* [web3.py GitHub](https://github.com/ethereum/web3.py)
* [web3.py Chat](https://gitter.im/ethereum/web3.py)

**Tatum -** _**The ultimate blockchain development platform.**_

* [Tatum](https://tatum.io/)
* [GitHub](https://github.com/tatumio/)
* [Documentation](https://docs.tatum.io/)
* [Discord](https://discord.gg/EDmW3kjTC9)

**web3j -** _**A Java/Android/Kotlin/Scala integration library for Ethereum.**_

* [GitHub](https://github.com/web3j/web3j)
* [Docs](https://docs.web3j.io/)
* [Gitter](https://gitter.im/web3j/web3j)

#### Blockchain services <a href="#blockchain-services" id="blockchain-services"></a>

**BlockCypher -** _**Ethereum Web APIs.**_

* [blockcypher.com](https://www.blockcypher.com/)
* [Documentation](https://www.blockcypher.com/dev/ethereum/)

**Chainbase -** _**All-in-one web3 data infrastructure for Ethereum.**_

* [chainbase.com](https://chainbase.com/)
* [Documentation](https://docs.chainbase.com/)
* [Discord](https://discord.gg/Wx6qpqz4AF)

**Chainstack -** _**Elastic and dedicated Ethereum nodes as a service.**_

* [chainstack.com](https://chainstack.com)
* [Documentation](https://docs.chainbase.com/docs)
* [Ethereum API reference](https://docs.chainstack.com/reference/ethereum-getting-started)

**Coinbase Cloud Node -** _**Blockchain Infrastructure API.**_

* [Coinbase Cloud Node](https://www.coinbase.com/cloud)
* [Documentation](https://docs.cloud.coinbase.com/)

**DataHub by Figment -** _**Web3 API services with Ethereum Mainnet and testnets.**_

* [DataHub](https://www.figment.io/)
* [Documentation](https://docs.figment.io/)

**Moralis -** _**Enterprise-Grade EVM API Provider.**_

* [moralis.io](https://moralis.io)
* [Documentation](https://docs.moralis.io/)
* [GitHub](https://github.com/MoralisWeb3)
* [Discord](https://moralis.io/joindiscord/)
* [Forum](https://forum.moralis.io/)

**NFTPort -** _**Ethereum Data and Mint APIs.**_

* [nftport.xyz](https://www.nftport.xyz/)
* [Documentation](https://docs.nftport.xyz/)
* [GitHub](https://github.com/nftport/)
* [Discord](https://discord.com/invite/K8nNrEgqhE)

**Tokenview -** _**The General Multi-Crypto Blockchain APIs Platform.**_

* [services.tokenview.io](https://services.tokenview.io/)
* [Documentation](https://services.tokenview.io/docs?type=api)
* [GitHub](https://github.com/Tokenview)

**Watchdata -** _**Provide simple and reliable API access to Ethereum blockchain.**_

* [Watchdata](https://watchdata.io/)
* [Documentation](https://docs.watchdata.io/)
* [Discord](https://discord.com/invite/TZRJbZ6bdn)

**Covalent -** _**Enriched blockchain APIs for 200+ Chains.**_

* [covalenthq.com](https://www.covalenthq.com/)
* [Documentation](https://www.covalenthq.com/docs/api/)
* [GitHub](https://github.com/covalenthq)
* [Discord](https://www.covalenthq.com/discord/)

### Further reading <a href="#further-reading" id="further-reading"></a>

_Know of a community resource that helped you? Edit this page and add it!_

### Related topics <a href="#related-topics" id="related-topics"></a>

* [Nodes and clients](../../nodes-and-clients/)
* [Development frameworks](../../frameworks/)

### Related tutorials <a href="#related-tutorials" id="related-tutorials"></a>

* [Set up Web3js to use the Ethereum blockchain in JavaScript](../../../tutorials/set-up-web3js-to-use-ethereum-in-javascript/) _– Instructions for getting web3.js set up in your project._
* [Calling a smart contract from JavaScript](../../../tutorials/calling-a-smart-contract-from-javascript/) _– Using the DAI token, see how to call contracts function using JavaScript._

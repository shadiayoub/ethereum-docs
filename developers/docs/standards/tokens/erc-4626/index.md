---
title: ERC-4626 Tokenized Vault Standard
lang: en
description: A standard for yield bearing vaults.
---

# ERC-4626 Tokenized Vault Standard

### Introduction <a href="#introduction" id="introduction"></a>

ERC-4626 is a standard to optimize and unify the technical parameters of yield-bearing vaults. It provides a standard API for tokenized yield-bearing vaults that represent shares of a single underlying ERC-20 token. ERC-4626 also outlines an optional extension for tokenized vaults utilizing ERC-20, offering basic functionality for depositing, withdrawing tokens and reading balances.

**The role of ERC-4626 in yield-bearing vaults**

Lending markets, aggregators, and intrinsically interest-bearing tokens help users find the best yield on their crypto tokens by executing different strategies. These strategies are done with slight variation, which might be error-prone or waste development resources.

ERC-4626 in yield-bearing vaults will lower the integration effort and unlock access to yield in various applications with little specialized effort from developers by creating more consistent and robust implementation patterns.

The ERC-4626 token is described fully in [EIP-4626](https://eips.ethereum.org/EIPS/eip-4626).

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

To better understand this page, we recommend you first read about [token standards](../) and [ERC-20](../erc-20/).

### ERC-4626 Functions and Features: <a href="#body" id="body"></a>

#### Methods <a href="#methods" id="methods"></a>

**asset**

```solidity
function asset() public view returns (address assetTokenAddress)
```

This function returns the address of the underlying token used for the vault for accounting, depositing, withdrawing.

**totalAssets**

```solidity
function totalAssets() public view returns (uint256)
```

This function returns the total amount of underlying assets held by the vault.

**convertToShares**

```solidity
function convertToShares(uint256 assets) public view returns (uint256 shares)
```

This function returns the amount of `shares` that would be exchanged by the vault for the amount of `assets` provided.

**convertToAssets**

```solidity
function convertToAssets(uint256 shares) public view returns (uint256 assets)
```

This function returns the amount of `assets` that would be exchanged by the vault for the amount of `shares` provided.

**maxDeposit**

```solidity
function maxDeposit(address receiver) public view returns (uint256 maxAssets)
```

This function returns the maximum amount of underlying assets that can be deposited in a single [`deposit`](index.md#deposit) call by the `receiver`.

**previewDeposit**

```solidity
function previewDeposit(uint256 assets) public view returns (uint256 shares)
```

This function allows users to simulate the effects of their deposit at the current block.

**deposit**

```solidity
function deposit(uint256 assets, address receiver) public returns (uint256 shares)
```

This function deposits `assets` of underlying tokens into the vault and grants ownership of `shares` to `receiver`.

**maxMint**

```solidity
function maxMint(address receiver) public view returns (uint256 maxShares)
```

This function returns the maximum amount of shares that can be minted in a single [`mint`](index.md#mint) call by the `receiver`.

**previewMint**

```solidity
function previewMint(uint256 shares) public view returns (uint256 assets)
```

This function allows users to simulate the effects of their mint at the current block.

**mint**

```solidity
function mint(uint256 shares, address receiver) public returns (uint256 assets)
```

This function mints exactly `shares` vault shares to `receiver` by depositing `assets` of underlying tokens.

**maxWithdraw**

```solidity
function maxWithdraw(address owner) public view returns (uint256 maxAssets)
```

This function returns the maximum amount of underlying assets that can be withdrawn from the `owner` balance with a single [`withdraw`](index.md#withdraw) call.

**previewWithdraw**

```solidity
function previewWithdraw(uint256 assets) public view returns (uint256 shares)
```

This function allows users to simulate the effects of their withdrawal at the current block.

**withdraw**

```solidity
function withdraw(uint256 assets, address receiver, address owner) public returns (uint256 shares)
```

This function burns `shares` from `owner` and send exactly `assets` token from the vault to `receiver`.

**maxRedeem**

```solidity
function maxRedeem(address owner) public view returns (uint256 maxShares)
```

This function returns the maximum amount of shares that can be redeemed from the `owner` balance through a [`redeem`](index.md#redeem) call.

**previewRedeem**

```solidity
function previewRedeem(uint256 shares) public view returns (uint256 assets)
```

This function allows users to simulate the effects of their redemption at the current block.

**redeem**

```solidity
function redeem(uint256 shares, address receiver, address owner) public returns (uint256 assets)
```

This function redeems a specific number of `shares` from `owner` and sends `assets` of underlying token from the vault to `receiver`.

**totalSupply**

```solidity
function totalSupply() public view returns (uint256)
```

Returns the total number of unredeemed vault shares in circulation.

**balanceOf**

```solidity
function balanceOf(address owner) public view returns (uint256)
```

Returns the total amount of vault shares the `owner` currently has.

#### Map of the interface <a href="#mapoftheinterface" id="mapoftheinterface"></a>

![Map of the ERC-4626 interface](../../../../../public/content/developers/docs/standards/tokens/erc-4626/map-of-erc-4626.png)

#### Events <a href="#events" id="events"></a>

**Deposit Event**

**MUST** be emitted when tokens are deposited into the vault via the [`mint`](index.md#mint) and [`deposit`](index.md#deposit) methods

```solidity
event Deposit(
    address indexed sender,
    address indexed owner,
    uint256 assets,
    uint256 shares
)
```

Where `sender` is the user who exchanged `assets` for `shares`, and transferred those `shares` to `owner`.

**Withdraw Event**

**MUST** be emitted when shares are withdrawn from the vault by a depositor in the [`redeem`](index.md#redeem) or [`withdraw`](index.md#withdraw) methods.

```solidity
event Withdraw(
    address indexed sender,
    address indexed receiver,
    address indexed owner,
    uint256 assets,
    uint256 shares
)
```

Where `sender` is the user who triggered the withdrawal and exchanged `shares`, owned by `owner`, for `assets`. `receiver` is the user who received the withdrawn `assets`.

### Further reading <a href="#further-reading" id="further-reading"></a>

* [EIP-4626: Tokenized vault Standard](https://eips.ethereum.org/EIPS/eip-4626)
* [ERC-4626: GitHub Repo](https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC4626.sol)

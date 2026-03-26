# COTI Privacy Portal

### Introduction

As the decentralized web matures, the necessity for robust, scalable financial privacy has become paramount. Traditional blockchain networks mandate total transparency, exposing all transaction histories and asset balances to public surveillance.

COTI V2 addresses a critical need by deploying an advanced, EVM-compatible Layer 2 network utilizing Garbled Circuits. The Privacy Portal serves as the primary interface for interacting with privacy-protected digital assets, granting complete control over financial data.



### Core Functionalities&#x20;

The Privacy Portal enables a seamless transition from transparent ledgers to encrypted sovereignty. The **COTI Privacy Portal** enables:<br>

* Token Minting: Seamlessly convert selected public ERC20 tokens into Private Tokens.
* Secure Storage: Manage private balances securely via the COTI MetaMask Snap.
* Confidential Transfers: Move assets between COTI wallets with full privacy regarding transaction amounts.
* On-Chain Encryption: Balances remain encrypted on the ledger, visible only to the authorized key holder.

### Why COTI V2 for Privacy?

Unlike standard ERC20 tokens where balances are public, COTI’s PrivateERC20 stores all data as ciphertexts.<br>

1. Garbled Circuits & MPC: Using the [`MpcCore`](../how-coti-works/advanced-topics/precompiles.md) precompile (address `0x64`), the network performs arithmetic (addition, subtraction, comparisons) on encrypted numbers without ever revealing the underlying plaintext to validators.&#x20;
2. Local Decryption: Your private balances are decrypted locally in your browser using a personal AES key stored in the [COTI Snap](../build-on-coti/guides/setting-up-coti-snap-with-your-metamask-wallet.md). No one else can see your holdings.
3. EVM Compatibility: Developers can extend the [`PrivateERC20`](developer-guide/privateerc20.sol.md) abstract contract to create private versions of any token using familiar tools like Hardhat and Solidity.


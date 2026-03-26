# 🧑‍💻 Developer Guide

This guide is for developers who want to build, integrate, and interact with [`PrivateERC20`](https://github.com/coti-io/coti-contracts/blob/main/contracts/token/PrivateERC20/PrivateERC20.sol) tokens on COTI.

Whether you're:

- deploying a new private token
- integrating privacy into an existing smart contract
- writing tests
- connecting a frontend application

this guide walks you through the full development process.

## What you'll learn

By the end of this guide, you will be able to:

- understand what `PrivateERC20` is and how it differs from a standard ERC20
- create your own private token by extending the base contract
- mint and burn tokens using both public and encrypted amounts
- work with encrypted balances
- send encrypted transactions from a React app using ethers.js and the COTI SDK

## What this guide covers

This guide follows the full lifecycle of a `PrivateERC20` token:

- understanding the `PrivateERC20` model
- creating and deploying a private token
- minting and burning tokens
- reading encrypted balances
- sending encrypted transactions from a frontend

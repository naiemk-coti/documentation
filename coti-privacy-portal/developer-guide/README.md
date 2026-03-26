# Developer Guide

This guide is intended for developers who want to build on top of the COTI [PrivateERC20](ttps://github.com/coti-io/coti-contracts/blob/main/contracts/token/PrivateERC20/PrivateERC20.sol)  standard contract — whether that means deploying a new private token, integrating one into a smart contract, writing tests, or connecting it to a React frontend.

It covers the full lifecycle of a `PrivateERC20` token:

* What `PrivateERC20` is and how it differs from a standard ERC20
* How to create your own private token by extending the base contract
* How to mint and burn tokens using both public and encrypted amounts
* How to read encrypted balances and send encrypted transactions from a React app using ethers.js and the COTI SDK&#x20;

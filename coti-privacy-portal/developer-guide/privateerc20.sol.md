# PrivateERC20.sol

### Overview

[PrivateERC20](ttps://github.com/coti-io/coti-contracts/blob/main/contracts/token/PrivateERC20/PrivateERC20.sol) is an abstract Solidity contract that implements a **fully encrypted ERC20 token** on the COTI network. Unlike a standard ERC20 where balances and transfer amounts are publicly visible on-chain, `PrivateERC20` stores all balances and allowances as **ciphertexts** — encrypted values that only the token holder can read using their personal AES key. All transactions, including transfers, mints, and burns, operate on these encrypted values.

Encryption and arithmetic on encrypted values is handled by COTI's  [`MpcCore`](../../how-coti-works/advanced-topics/precompiles.md)  precompile at `address(0x64)`. This precompile allows the network to perform operations like addition, subtraction, and comparison on encrypted numbers without ever revealing the underlying plaintext — not even to validators.

### What makes it different from a standard ERC20

The following table highlights the key technical and functional deviations between a transparent ERC20 and its private counterpart:

| Feature                            | Standard ERC20 | PrivateERC20                            |
| ---------------------------------- | -------------- | --------------------------------------- |
| Balances visible on-chain          | Yes (public)   | No (encrypted ciphertext)               |
| Transfer amounts visible           | Yes            | No                                      |
| Allowances visible                 | Yes            | No                                      |
| `totalSupply()` returns real value | Yes            | No — always returns `0` for privacy     |
| Requires AES key to read balance   | No             | Yes                                     |
| Supports plain uint256 operations  | Yes            | Yes                                     |
| Supports encrypted operations      | No             | Yes (`itUint256`, `gtUint256` variants) |

### How it works

1. **Balances** are stored as `ctUint256` (ciphertext) on-chain. Each account has two ciphertext slots: one for the MPC network (`ciphertext`) and one re-encrypted for the user (`userCiphertext`).
2. **Transfers** go through the MPC precompile via `MpcCore.transfer()`, which atomically updates both sender and receiver balances in encrypted form.
3. **Reading your balance** requires calling `balanceOf(address)` to get the ciphertext, then decrypting it client-side with your AES key using the COTI SDK.
4. **Writing encrypted amounts** (transfer, approve, mint, burn) requires building an `itUint256` — an AES-encrypted value plus an ECDSA signature — so the MPC network can verify the input came from the rightful key holder.
5. **Onboarding** registers your wallet's encryption address on-chain via `setAccountEncryptionAddress()`, enabling the contract to re-encrypt your balance specifically for your AES key.\
   <br>

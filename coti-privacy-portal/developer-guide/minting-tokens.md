# Minting Tokens

There are three mint variants depending on how the amount is provided.

{% stepper %}
{% step %}
#### Public Mint (plain uint256)

Requires `publicAmountsEnabled = true` (default). Only callable by `MINTER_ROLE`.

```javascript
// Hardhat / ethers.js
const token = await hre.ethers.getContractAt("MyPrivateToken", tokenAddress);
const amount = hre.ethers.parseUnits("1000", 18); // 1000 tokens
const tx = await token.mint(recipientAddress, amount, { gasLimit: 5000000 });
await tx.wait();
console.log("Minted 1000 tokens to", recipientAddress);
```
{% endstep %}

{% step %}
#### Encrypted Mint (itUint256)

For privacy-preserving minting where the amount is encrypted by the caller.

```javascript
const { prepareIT256 } = require("@coti-io/coti-sdk-typescript");

// Build the encrypted amount
const MINT_SELECTOR = ethers.id("mint(address,((uint256,uint256),bytes))").slice(0, 10);
const wallet = new ethers.Wallet(process.env.PRIVATE_KEY, ethers.provider);

const it = prepareIT256(
    BigInt(hre.ethers.parseUnits("500", 18)), // 500 tokens
    { wallet, userKey: process.env.PRIVATE_AES_KEY_TESTNET },
    tokenAddress,
    MINT_SELECTOR
);

const tx = await token.connect(minterSigner)["mint(address,((uint256,uint256),bytes))"](
    recipientAddress,
    [[it.ciphertext.ciphertextHigh, it.ciphertext.ciphertextLow], it.signature],
    { gasLimit: 5000000 }
);
await tx.wait();
```
{% endstep %}

{% step %}
#### GT Mint (gtUint256)

For contract-to-contract flows where you already hold a `gtUint256` in memory (e.g. inside a bridge contract).

```solidity
// Inside a Solidity contract with MINTER_ROLE
gtUint256 gtAmount = MpcCore.setPublic256(depositAmount);
gtBool success = token.mintGt(recipient, gtAmount);
require(MpcCore.decrypt(success), "Mint failed");
```
{% endstep %}
{% endstepper %}

{% hint style="warning" %}
Note: `mintGt` and `mint(itUint256)` return `gtBool` — they do NOT revert on failure. Always decrypt and check the return value.
{% endhint %}

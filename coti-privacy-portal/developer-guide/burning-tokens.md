# Burning Tokens

Three burn variants mirror the mint variants. Burns are called by the token holder (no role required).

{% stepper %}
{% step %}
### Public Burn

```javascript
const amount = hre.ethers.parseUnits("100", 18);
const tx = await token.connect(holderSigner)["burn(uint256)"](amount, { gasLimit: 5000000 });
await tx.wait();
```
{% endstep %}

{% step %}
### Encrypted Burn (itUint256)

```javascript
const BURN_SELECTOR = ethers.id("burn(((uint256,uint256),bytes))").slice(0, 10);

const it = prepareIT256(
    BigInt(hre.ethers.parseUnits("100", 18)),
    { wallet, userKey: process.env.PRIVATE_AES_KEY_TESTNET },
    tokenAddress,
    BURN_SELECTOR
);

const tx = await token.connect(holderSigner)["burn(((uint256,uint256),bytes))"](
    [[it.ciphertext.ciphertextHigh, it.ciphertext.ciphertextLow], it.signature],
    { gasLimit: 5000000 }
);
await tx.wait();
```
{% endstep %}

{% step %}
### GT Burn

```solidity
// Inside a Solidity contract
gtUint256 gtAmount = MpcCore.setPublic256(withdrawAmount);
gtBool success = token.burnGt(gtAmount);
require(MpcCore.decrypt(success), "Burn failed");
```
{% endstep %}
{% endstepper %}

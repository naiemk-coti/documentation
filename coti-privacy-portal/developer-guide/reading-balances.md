# Reading Balances

Balances are stored encrypted. There are two ways to read them.

#### balanceOf(address) — Returns ctUint256

Returns the on-chain ciphertext. You decrypt it client-side with your AES key.

```javascript
// ABI fragment
const abi = [
    "function balanceOf(address) view returns (tuple(uint256 ciphertextHigh, uint256 ciphertextLow))"
];
const contract = new ethers.Contract(tokenAddress, abi, provider);
const encryptedBalance = await contract.balanceOf(userAddress);

// Decrypt with COTI SDK
const { decryptUint256 } = require("@coti-io/coti-sdk-typescript");
const decrypted = decryptUint256(
    { ciphertextHigh: encryptedBalance.ciphertextHigh, ciphertextLow: encryptedBalance.ciphertextLow },
    aesKeyHex  // 32 hex char string, no 0x prefix
);

const formatted = ethers.formatUnits(decrypted, 18);
console.log("Balance:", formatted);
```

#### Encryption Address (setAccountEncryptionAddress)

Before your balance can be re-encrypted for you to read, you must register your encryption address. This is typically your wallet address.

```javascript
const tx = await token.connect(signer).setAccountEncryptionAddress(signer.address, { gasLimit: 5000000 });
await tx.wait();
```

> > This is called "onboarding" in the COTI ecosystem.&#x20;


# Troubleshooting

### Common Errors and Fixes

| Error                             | Cause                                         | Fix                                                   |
| --------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| `PublicAmountsDisabled`           | `publicAmountsEnabled` is false               | Use encrypted variants or re-enable via admin         |
| `ERC20SelfTransferNotAllowed`     | `from == to` in transfer                      | Never transfer to yourself                            |
| `ERC20InvalidMetadata`            | Empty name or symbol in constructor           | Pass non-empty strings                                |
| `ERC20: mint failed`              | MPC precompile returned false                 | Check MPC network status; retry                       |
| `AES key mismatch`                | Wrong AES key used to decrypt                 | Re-onboard wallet to get correct key                  |
| `invalid BytesLike value`         | Passing HardhatEthersSigner to `prepareIT256` | Use `new ethers.Wallet(privateKey, provider)` instead |
| `PRIVATE_AES_KEY_TESTNET not set` | Missing env var                               | Add 32 hex char key to `.env` (no `0x` prefix)        |

### Security Notes

* Deploy only on chains where the MPC precompile at `address(0x64)` is trusted.
* `MINTER_ROLE` must only be granted to audited contracts&#x20;
* Encrypted operations (`mintGt`, `burnGt`, `transferGT`) return `gtBool` and do NOT revert on failure — always decrypt and check the return value.
* Self-transfers (`from == to`) are explicitly blocked at the contract level.
* `transferAndCall` is protected by `nonReentrant` but the callback contract must be trusted.
* `totalSupply()` always returns `0` for privacy — do not rely on it for supply accounting.

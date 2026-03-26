# Architecture

The diagram below shows how `PrivateERC20` is composed. It inherits from standard OpenZeppelin contracts for access control and reentrancy protection, and delegates all encrypted arithmetic to the MPC precompile via the `MpcCore` library.

```
PrivateERC20 (abstract)
├── inherits: Context, ERC165, IPrivateERC20, AccessControl, ReentrancyGuard
├── storage:
│   ├── _balances: mapping(address => utUint256)   // { ciphertext, userCiphertext }
│   ├── _allowances: mapping(address => mapping(address => Allowance))
│   ├── _totalSupply: ctUint256                    // always decrypts to real supply
│   └── _accountEncryptionAddress: mapping(address => address)
├── roles:
│   ├── DEFAULT_ADMIN_ROLE  → deployer
│   └── MINTER_ROLE         → bridges, vesting contracts, etc.
└── MpcCore (precompile at 0x64)
    ├── setPublic256(uint256) → gtUint256
    ├── validateCiphertext(itUint256) → gtUint256
    ├── transfer(gt, gt, gt) → (gt, gt, gtBool)
    ├── offBoard(gtUint256) → ctUint256
    ├── offBoardToUser(gtUint256, address) → ctUint256
    └── onBoard(ctUint256) → gtUint256
```

### Key Concepts

Understanding  COTI data types is essential before working with any `PrivateERC20` function. Every encrypted operation moves values through these representations in a specific order: user input arrives as `itUint256`, gets loaded into `gtUint256` for in-memory computation, and is stored back on-chain as `ctUint256`.

| Term        | Description                                                                                |
| ----------- | ------------------------------------------------------------------------------------------ |
| `gtUint256` | Garbled-text uint256 — an in-memory encrypted value used during MPC computation            |
| `ctUint256` | Ciphertext uint256 — an encrypted value stored on-chain                                    |
| `itUint256` | Input-text uint256 — an encrypted value submitted by a user (ciphertext + signature)       |
| `MpcCore`   | Solidity library that calls the COTI MPC precompile at `address(0x64)`                     |
| AES Key     | A 16-byte (32 hex char) key derived per wallet during onboarding, used to decrypt balances |


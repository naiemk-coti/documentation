# Creating a New PrivateERC20 Token

`PrivateERC20` is an `abstract` contract. To create a new token, extend it and call the constructor with a name and symbol.

#### Minimal Token

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "../PrivateERC20.sol";

contract MyPrivateToken is PrivateERC20 {
    constructor() PrivateERC20("My Private Token", "p.MYT") {}
}
```

That's it. The deployer automatically receives `DEFAULT_ADMIN_ROLE` and `publicAmountsEnabled` is set to `true`.

#### Custom Decimals

Override `decimals()` to change from the default 18:

```solidity
contract PrivateTetherUSD is PrivateERC20 {
    constructor() PrivateERC20("Private Tether USD", "p.USDT") {}

    function decimals() public view virtual override returns (uint8) {
        return 6;
    }
}
```

#### Role-Based Access

`PrivateERC20` uses OpenZeppelin `AccessControl`:

| Role                 | Constant                   | Purpose                                                                    |
| -------------------- | -------------------------- | -------------------------------------------------------------------------- |
| `DEFAULT_ADMIN_ROLE` | `0x00`                     | Granted to deployer; can grant/revoke roles, toggle `publicAmountsEnabled` |
| `MINTER_ROLE`        | `keccak256("MINTER_ROLE")` | Required to call `mint()`                                                  |

Grant `MINTER_ROLE` to contract administrator

```solidity
// Solidity
bytes32 MINTER_ROLE = keccak256("MINTER_ROLE");
token.grantRole(MINTER_ROLE, bridgeAddress);
```

```javascript
// Hardhat script
const MINTER_ROLE = hre.ethers.id("MINTER_ROLE");
const tx = await token.grantRole(MINTER_ROLE, bridgeAddress, { gasLimit: 5000000 });
await tx.wait();
```

#### Deploying with Hardhat

```javascript
// scripts/deploy-my-token.cjs
const hre = require("hardhat");

async function main() {
    const [deployer] = await hre.ethers.getSigners();
    console.log("Deploying with:", deployer.address);

    const Factory = await hre.ethers.getContractFactory("MyPrivateToken");
    const token = await Factory.deploy({ gasLimit: 12000000 });
    await token.waitForDeployment();

    const address = await token.getAddress();
    console.log("MyPrivateToken deployed at:", address);

    // Grant MINTER_ROLE to another address if needed
    const MINTER_ROLE = hre.ethers.id("MINTER_ROLE");
    await token.grantRole(MINTER_ROLE, "0xYourMinterAddress");
}

main().catch(console.error);
```

```bash
npx hardhat run scripts/deploy-my-token.cjs --network cotiTestnet
```

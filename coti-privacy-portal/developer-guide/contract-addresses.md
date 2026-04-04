# Contract Addresses

Addresses for **COTI Testnet (7082400)** below match the Privacy Portal source of truth: `coti-privacy-portal/src/contracts/config.ts`. They may change after contract redeploys; always verify against that file or your deployed environment.

#### COTI Testnet (Chain ID: 7082400)

| Token                | Address                                      |
| -------------------- | -------------------------------------------- |
| p.COTI (PrivateCoti) | `0x6cE8907414986E73De9e7D28d62Ea2080F8E88E1` |
| p.WETH               | `0xF009BADb181d471995a1CFF406C3Db7B180F64eA` |
| p.WBTC               | `0xB50F1680a4C69145ABc09A2A71c8D5b8051578cF` |
| p.USDT               | `0xcEF137E96eDF68EE99D4CdEa7085f154d74895cD` |
| p.USDC.e             | `0x37f78dcCd15876F74391EF1F01b76557D9FF1dea` |
| p.WADA               | `0x1245f50a3E9129A219b4bf66D10fEaEA47467B69` |
| p.gCOTI              | `0x1503b02a4Aa27812306c65116FD23b733603F142` |

#### COTI Testnet — Public Tokens

| Token  | Address                                      |
| ------ | -------------------------------------------- |
| WETH   | `0x8bca4e6bbE402DB4aD189A316137aD08206154FB` |
| WBTC   | `0x5dBDb2E5D51c3FFab5D6B862Caa11FCe1D83F492` |
| USDT   | `0x9e961430053cd5AbB3b060544cEcCec848693Cf0` |
| USDC.e | `0x63f3D2Cc8F5608F57ce6E5Aa3590A2Beb428D19C` |
| WADA   | `0xe3E2cd3Abf412c73a404b9b8227B71dE3CfE829D` |
| gCOTI  | `0x878a42D3cB737DEC9E6c7e7774d973F46fd8ed4C` |

#### COTI Testnet — Bridges

| Bridge                  | Address                                      |
| ----------------------- | -------------------------------------------- |
| PrivacyBridgeCotiNative | `0x13d1F98236eC09219b009d843F8A7f150fDc9E46` |
| PrivacyBridgeWETH       | `0x56192C1Bf282DA179F48241be2054bF432Ed2A7C` |
| PrivacyBridgeWBTC       | `0x4298bEcBa4f31bA90B36EaCDf97D198c814dBfE5` |
| PrivacyBridgeUSDT       | `0xc0062799Ed7e7D3A8AFE5A6493b1b1DC74eE98fE` |
| PrivacyBridgeUSDCe      | `0xBB6cE68F692B8B2eE95Bfa8B22947F1c577F92B0` |
| PrivacyBridgeWADA       | `0x29BbF8E9Db86803837e19Cb4009AFFe0cEec1044` |
| PrivacyBridgegCOTI      | `0x3CFc34C728ECAf70c9723D3e3718137236e88715` |

#### COTI Mainnet (Chain ID: 2632500)

Mainnet **native private COTI** bridge and **public** token addresses from the same portal config:

| Role / token              | Address                                      |
| ------------------------- | -------------------------------------------- |
| p.COTI (PrivateCoti)      | `0x143705349957A236d74e0aDb5673F880fEDB101f` |
| PrivacyBridgeCotiNative   | `0x6056bFE6776df4bEa7235A19f6D672089b4cdBeB` |
| WETH                      | `0x639aCc80569c5FC83c6FBf2319A6Cc38bBfe26d1` |
| WBTC                      | `0x8C39B1fD0e6260fdf20652Fc436d25026832bfEA` |
| USDT                      | `0xfA6f73446b17A97a56e464256DA54AD43c2Cbc3E` |
| USDC.e                    | `0xf1Feebc4376c68B7003450ae66343Ae59AB37D3C` |
| WADA                      | `0xe757Ca19d2c237AA52eBb1d2E8E4368eeA3eb331` |
| gCOTI                     | `0x7637C7838EC4Ec6b85080F28A678F8E234bB83D1` |

In the current portal `config.ts`, **wrapped private tokens** (`p.WETH`, `p.USDT`, etc.) and **ERC20 privacy bridges** on mainnet are empty placeholders until deployed. Use the repository’s `config.ts` for updates.

### Token Decimals Reference

| Token                    | Decimals |
| ------------------------ | -------- |
| p.COTI, p.WETH, p.gCOTI  | 18       |
| p.WBTC                   | 8        |
| p.USDT, p.USDC.e, p.WADA | 6        |

# QA Report for PoolTogether contest
## Overview
During the audit, 3 non-critical issues were found.
№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | Order of Layout | Non-Critical | 2
NC-2 | Typos | Non-Critical | 5
NC-3 | Maximum line length exceeded | Non-Critical | 3

## Non-Critical Risk Findings(3)
### NC-1. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
- https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L28-L40
- https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L24-L31

##### Recommendation
Events should be placed after state variables.

#
### NC-2. Typos
##### Instances
- EthereumToArbitrumRelayer.sol#L22: [```* @param nonce Nonce to uniquely idenfity the batch of calls```](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L22) => ```identify```
- EthereumToArbitrumRelayer.sol#L39: [```/// @notice Nonce to uniquely idenfity each batch of calls.```](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L39) => ```identify```
- EthereumToOptimismRelayer.sol#L28: [```/// @notice Nonce to uniquely idenfity each batch of calls.```](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L28) => ```identify```
- EthereumToPolygonRelayer.sol#L22: [```/// @notice Nonce to uniquely idenfity each batch of calls.```](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L22) => ```identify```
- CallLib.sol#L44: [```* @param _nonce Nonce to uniquely idenfity the batch of calls```](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L44) => ```identify```

#
### NC-3. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.
##### Instances
- https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L14
- https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L14
- https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L14

##### Recommendation
Make the lines shorter.
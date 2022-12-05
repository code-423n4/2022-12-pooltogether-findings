# QA Report

1. In each relayer contract, `relayCalls()` is payable, however the protocol has not implemented a way to handle payment.
   There is currently no way to retrieve any ether trapped in the contract.

Instances: 4

https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L67
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L101
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L49
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L45

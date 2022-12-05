
## 01 MISSING EVENTS FOR ONLY FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

The functions that change critical parameters should emit events.

### Proof of Concept

_There are 4 instances of this issue_

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol

```
File: src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol

52: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

```
File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

139: function setExecutor(ICrossChainExecutor _executor) external {
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol

```
File: src/ethereum-optimism/EthereumToOptimismExecutor.sol

66: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol

```
File: src/ethereum-optimism/EthereumToOptimismExecutor.sol

85: function setExecutor(ICrossChainExecutor _executor) external {
```

### Recommended Mitigation Steps

Add events to all functions that change critical parameters.

----------------

## 02 USE A MORE RECENT VERSION OF SOLIDITY

It's a best practice to use the latest compiler version.

The specified minimum compiler version is quite old. Older compilers might be susceptible to some bugs. We recommend changing the solidity version pragma to the latest version to enforce the use of an up-to-date compiler.

A list of known compiler bugs and their severity can be found here: [https://etherscan.io/solcbuginfo](https://etherscan.io/solcbuginfo)

### Proof of Concept

This issue exists in all the In-scope contracts

--------------

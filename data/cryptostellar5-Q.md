### 01 MISSING EVENTS FOR ONLY FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

*Number of Instances Identified: 4*

The functions that change critical parameters should emit events.

Add events to all functions that change critical parameters.

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol

```
52: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

```
139: function setExecutor(ICrossChainExecutor _executor) external {
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol

```
66: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol

```
85: function setExecutor(ICrossChainExecutor _executor) external {
```


### 02 USE A MORE RECENT VERSION OF SOLIDITY

This is applicable to all the in-scope smart contracts

It's a best practice to use the latest compiler version.

We recommend changing the solidity version pragma to the latest version to enforce the use of an up to date compiler.

List of known compiler bugs and their severity can be found here: https://etherscan.io/solcbuginfo


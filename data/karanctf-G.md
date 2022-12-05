
## Use `!= 0` instead of `> 0`
```solidity
src/ethereum-polygon/EthereumToPolygonRelayer.sol:38:    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
src/ethereum-optimism/EthereumToOptimismRelayer.sol:40:    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```
## Use custom errors to save deployment and runtime costs in case of revert
Instead of using strings for error messages (e.g., `require(msg.sender == owner, “unauthorized”)`), you can use custom errors to reduce both deployment and runtime gas costs. In addition, they are very convenient as you can easily pass dynamic information to them.
```solidity
src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:53:    require(address(relayer) == address(0), "Executor/relayer-already-set");
src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:65:    require(
src/ethereum-polygon/EthereumToPolygonRelayer.sol:38:    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
src/ethereum-optimism/EthereumToOptimismExecutor.sol:38:    require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");
src/ethereum-optimism/EthereumToOptimismExecutor.sol:67:    require(address(relayer) == address(0), "Executor/relayer-already-set");
src/ethereum-optimism/EthereumToOptimismExecutor.sol:80:    require(
src/ethereum-optimism/EthereumToOptimismRelayer.sol:39:    require(address(_crossDomainMessenger) != address(0), "Relayer/CDM-not-zero-address");
src/ethereum-optimism/EthereumToOptimismRelayer.sol:40:    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
src/ethereum-optimism/EthereumToOptimismRelayer.sol:86:    require(address(executor) == address(0), "Relayer/executor-already-set");
```

## Use `calldata` instead of `memory` when used only for reading
**forat virable declare time only
```solidity
src/ethereum-polygon/EthereumToPolygonRelayer.sol:74:  function _processMessageFromChild(bytes memory data) internal override {
```


## Use `abi.encodePacked` for gas optimization
Changing the abi.encode function to abi.encodePacked  can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
```solidity
src/ethereum-polygon/EthereumToPolygonRelayer.sol:60:    _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));
```




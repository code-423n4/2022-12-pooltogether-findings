### ++X IS MORE EFFICIENT THAN X++(SAVES ~6 GAS)

File: EthereumToArbitrumRelayer.sol [Line 78](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78)
```
    nonce++;
```
File: EthereumToOptimismRelayer.sol [Line 60](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60)
```
    nonce++;
```
File: EthereumToPolygonRelayer.sol [Line 56](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56)
```
    nonce++;
```
File: CallLib.sol [Line 61](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61)
```
    for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```
### USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

File: EthereumToArbitrumExecutor.sol [Line 53](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L53)
```
    require(address(relayer) == address(0), "Executor/relayer-already-set");
```
File: EthereumToArbitrumExecutor.sol [Line 65-67](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L65-L67)
```
    require(
      msg.sender == AddressAliasHelper.applyL1ToL2Alias(address(_relayer)),
      "Executor/sender-unauthorized"
```
File: EthereumToArbitrumRelayer.sol [Line 57](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L57)
```
    require(address(_inbox) != address(0), "Relayer/inbox-not-zero-address");
```
File: EthereumToArbitrumRelayer.sol [Line 58](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L58)
```
    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```
File: EthereumToArbitrumRelayer.sol [Line 109](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L109)
```
    require(relayed[_getTxHash(_nonce, _calls, _sender, _gasLimit)], "Relayer/calls-not-relayed");
```
File: EthereumToArbitrumRelayer.sol [Line 140](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L140)
```
    require(address(executor) == address(0), "Relayer/executor-already-set");
```
File: EthereumToOptimismExecutor.sol [Line 38](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L38)
```
    require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");
```
File: EthereumToOptimismExecutor.sol [Line 67](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L67)
```
    require(address(relayer) == address(0), "Executor/relayer-already-set");
```
File: EthereumToOptimismExecutor.sol [Line 80-84](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L80-L84)
```
    require(
      msg.sender == address(_crossDomainMessenger) &&
        _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
      "Executor/sender-unauthorized"
    );
```
File: EthereumToOptimismRelayer.sol [Line 39](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L39)
```
    require(address(_crossDomainMessenger) != address(0), "Relayer/CDM-not-zero-address");
```
File: EthereumToOptimismRelayer.sol [Line 40](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L40)
```
    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```
File: EthereumToOptimismRelayer.sol [Line 86](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L86)
```
    require(address(executor) == address(0), "Relayer/executor-already-set");
```
File: EthereumToPolygonRelayer.sol [Line 38](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L38)
```
    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```
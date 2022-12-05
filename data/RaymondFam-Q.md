## Comment and Logic Mismatch
The @dev comments pertaining to `setRelayer()` and `setExecuter()` unanimously allege that the function will revert if the relayer or executer contract address has already been set. However, each of these functions would only revert if the address of the parameter input was a zero address. Consider changing the comments and/or refactoring the function logic to match up with the intended design.

Here are the four instances entailed:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L134-L142
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L47-L55
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L80-L88
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L61-L69

## Complementary Codehash Checks
Consider adding an optional codehash check for each zero address check pertaining to the immutable variable at the constructor since the latter cannot guarantee a matching input of contract address has been entered.

For instance, the following code block may be refactored as follows:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L56-L62

```
  constructor(IInbox _inbox, uint256 _maxGasLimit, bytes32 _inboxCodeHash) {
    require(address(_inbox) != address(0) && address(_inbox).codehash == _inboxCodeHash, "Relayer/inbox-not-zero-address");
    ...

    inbox = _inbox;
    ...
  }
```
All other two instances entailed:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L38-L44
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L37-L40

## Zero Address Checks
Zero address checks should be implemented at the constructor to avoid human error(s) that could result in non-functional calls associated with them particularly when the incidents involve immutable variables that could lead to contract redeployment at its worst. 

Here are the two instances entailed:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L34-L35
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L39

## Threshold Checks
Consider implementing some relevant boundary/threshold limits on the zero value checks particularly when this would entail immutable variables at the constructor.

For instance, `maxGasLimit` in the instance below could end up assigned an edge value of 1 or a very large integer simply because any input value of `_maxGasLimit != 0` is going to bypass the the zero value check.

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L56-L62

```
  constructor(IInbox _inbox, uint256 _maxGasLimit) {
    ...    
    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");

    ...
    maxGasLimit = _maxGasLimit;
  }
```
All other three instances entailed:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L40
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L40
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L38

## Missing Checks for Contract Existence
Performing a low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed as documented in the link below:

https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions

Consider check for target contract existence before call.

Here is one instance entailed:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L64

## Lack of Events for Critical Operations
Critical operations not triggering events will make it difficult to review the correct behavior of the deployed contracts. Users and blockchain monitoring systems will not be able to detect suspicious behaviors at ease without events. Consider adding events where appropriate for all critical operations for better support of off-chain logging API.

Here are the four instances entailed:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L134-L142
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L47-L55
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L80-L88
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L61-L69
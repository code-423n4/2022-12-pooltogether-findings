# 1. Clarify comment documenting ExecutorAware _nonce()
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/abstract/ExecutorAware.sol#L59
The comment states "@return _callDataNonce Nonce uniquely identifying the message that was executed". It would make sense to clarify that the nonce is unique to a batch of calls. In the context of ExecutorAware, "message" may be interpreted as a single message used to call a function of an ExecutorAware, and the nonce will not be unique to this message if multiple message calls are included in one batch of calls sharing a nonce.

# 2. CrossChainRelayerPolygon relayCalls() does not use gasLimit
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L45-L65
CrossChainRelayerPolygon's relayCalls() does not actually do anything with the gasLimit except record it in an event. (Ie the gas limit is not forwarded to the Polygon bridge). It would make sense to document this behavior and possibly require that the user specifies a sentinel gasLimit (eg zero) to make it clear the gasLimit is unused. In addition, the maxGasLimit state variable of CrossChainRelayerPolygon is unnecessary because the gasLimit provided to relayCalls() is not actually used for anything.

# 3. Clarify CallLib executeCalls() comment
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L43
The comment states "@dev Must emit the `ExecutedCalls` event once calls have been executed." The comment should be clarified to indicate that ExecutedCalls must be emitted by the _caller_ of executeCalls(). The previous comment lines describe executeCalls() itself rather than the caller of executeCalls() so indicating that this line about emitting ExecutedCalls refers to the caller of executeCalls() would improve clarity.

# 4. CallLib executeCalls() should check that call target exists
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L64
The EVM (and any EVM conforming chains) treat a call to a nonexistent contract as succeeding, and solidity's low level call() function does not check that the called contract exists. It would make sense for executeCalls() to check that that the target contract exists in order to ensure that the success check it does perform is not spurious and that the call did not mistakenly reference a nonexistent target.
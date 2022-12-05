# There is no point implementing the `setExecutor` (and `setRelayer`) function 

Since the `setExecutor` function can only be called once, which is to set the executor, meanwhile if the executor is not set, the `executeCalls` will always revert because not passing the `_isAuthorized()` function, so it's best just remove the `setExecutor` and directly set the executor on the constructor (and make immutable).

As simple as, if executor is yet to be known, just wait until it's known, then deployment the contract.

same logic with `setRelayer`, which is unnecessary

```solidity
66:   function setRelayer(ICrossChainRelayer _relayer) external {
67:     require(address(relayer) == address(0), "Executor/relayer-already-set");
68:     relayer = _relayer;
69:   }
```

```solidity
139:   function setExecutor(ICrossChainExecutor _executor) external {
140:     require(address(executor) == address(0), "Relayer/executor-already-set");
141:     executor = _executor;
142:   }
```
# using abi.encodedPacked which is not preferably be used because of ambiguity 

```solidity
File: CallLib.sol
64:       (bool _success, bytes memory _returnData) = _call.target.call(
65:         abi.encodePacked(_call.data, _nonce, _sender)
66:       );
```
abi.encodePacked is a non standard encoding that should preferably not be used due to its ambiguity with dynamic types, if you are not dealing with dynamic types and inputs which size is already multiple of 32 then they can both give the same output.

In general, the encoding is ambiguous as soon as there are two dynamically-sized elements, because of the missing length field.

# No check if `_calls` is empty (length is 0)

All of Executors (EthereumToArbitrumExecutor, EthereumToOptimismExecutor, EthereumToPolygonExecutor), when trying to call `CallLib.executeCalls` the `_calls` parameter input are not checked if it cointains call (length > 0) or not. 

We can just revert if the `_calls` length is 0

# No event of storage variable changes (setRelayer)

On `setRelayer` function, it doesnt have any emit event, it's good practice to emit an event when storage variable is changed.


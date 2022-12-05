# There is no point implementing the `setExecutor` function 

Since the `setExecutor` function can only be called once, which is to set the executor, meanwhile if the executor is not set, the `executeCalls` will always revert because not passing the `_isAuthorized()` function, so it's best just remove the `setExecutor` and directly set the executor on the constructor (and make immutable).

As simple as, if executor is yet to be known, just wait until it's known, then deployment the contract.

# No check if `_calls` is empty (length is 0)

All of Executors (EthereumToArbitrumExecutor, EthereumToOptimismExecutor, EthereumToPolygonExecutor), when trying to call `CallLib.executeCalls` the `_calls` parameter input are not checked if it cointains call (length > 0) or not. 

We can just revert if the `_calls` length is 0

# No event of storage variable changes (setRelayer)

On `setRelayer` function, it doesnt have any emit event, it's good practice to emit an event when storage variable is changed.


# No check if `_calls` is empty (length is 0)

All of Executors (EthereumToArbitrumExecutor, EthereumToOptimismExecutor, EthereumToPolygonExecutor), when trying to call `CallLib.executeCalls` the `_calls` parameter input are not checked if it cointains call (length > 0) or not. 

We can just revert if the `_calls` length is 0

# No event of storage variable changes (setRelayer)

On `setRelayer` function, it doesnt have any emit event, it's good practice to emit an event when storage variable is changed.


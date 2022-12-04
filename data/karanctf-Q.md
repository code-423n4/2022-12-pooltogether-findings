

1. Avoid using  `_isAuthorized` function in eth->arbitrum and eth->optimism
	@src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:37
```solidity
	ICrossChainRelayer _relayer = relayer; 
	_isAuthorized(_relayer); 
	bool _executedNonce = executed[_nonce]; 
	executed[_nonce] = true;
```

can be rewritten as:

```solidity
	require(   msg.sender == AddressAliasHelper.applyL1ToL2Alias(address(relayer)),   "Executor/sender-unauthorized" );
	bool _executedNonce = executed[_nonce]; executed[_nonce] = true;
```
or use `view` function modifier for the `_isAuthorized()` function, as it only reads data from the contract.

2. Check for 0 address 
    Add a require statement to check that the `_fxRoot` address provided to the constructor is not the zero address.
	@src/ethereum-polygon/EthereumToPolygonRelayer.sol:38
```solidity
	require(_fxRoot != address(0)
```
3. Replace the `maxGasLimit` variable with a `public constant` to improve readability and make it clear that the value cannot be changed.
4. Use the `emit` keyword to emit an event after the calls have been executed successfully. This would allow any contracts that are listening for this event to be notified that the calls have been executed.
	@src/libraries/CallLib.sol:69
```solidity
emit ExecutedCalls(_nonce, _sender, _calls);
```

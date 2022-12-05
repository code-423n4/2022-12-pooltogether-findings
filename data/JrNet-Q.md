### [1] No check for `_calls` array can waste gas for no reason

The `relayCalls` function does not check whether the _calls array is empty, which means that a user or contract could call the function with an empty array and waste gas for no reason. This can be fixed by adding a check for an empty array before incrementing the `nonce` variable and emitting the `RelayedCalls` event.

Instances:

Files: [src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L82-L84](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L82-L84)

```solidity
function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
    external
    payable
    returns (uint256)
{
  uint256 _maxGasLimit = maxGasLimit;
  if (_gasLimit > _maxGasLimit) {
    revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
  }

  nonce++;
  uint256 _nonce = nonce;
  relayed[_getTxHash(_nonce, _calls, msg.sender, _gasLimit)] = true;
  emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);
  return _nonce;
}
```

### [2] Chance of transaction to fail if the gas limit

The` relayCalls` function does not check whether the caller has sufficient balance to pay for the transaction. This means that **a user or contract could call the function with a high gas limit and a low gas price**, which could cause the transaction to fail if the gas limit is exceeded. 

Files: [src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol)

This can be fixed by adding a check for the caller's balance and comparing it to the estimated transaction cost.

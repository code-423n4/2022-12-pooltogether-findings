## EthereumToArbitrumRelayer::relayCalls() should not have payable decorator



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

It is recommended to remove the payable modifier
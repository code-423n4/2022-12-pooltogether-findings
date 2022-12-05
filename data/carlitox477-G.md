# CrossChainRelayerArbitrum#relayCalls; CrossChainRelayerOptimism#relayCalls; CrossChainRelayerPolygon#relayCalls : can avoid 1 access to nonce
Current implementation has 3 access to nonce, these can be reduced to just 2:
```solidity
uint256 _nonce = nonce + 1;
nonce = _nonce
```

# CrossChainRelayerArbitrum#relayCalls; CrossChainRelayerOptimism#relayCalls; CrossChainRelayerPolygon#relayCalls: maxGasLimit is a immutable, it should not be cached
In order to save gas, in ```relayCalls```:
```diff
-   uint256 _maxGasLimit = maxGasLimit;

-   if (_gasLimit > _maxGasLimit) {
-      revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
    if (_gasLimit > maxGasLimit) {
      revert GasLimitTooHigh(_gasLimit, maxGasLimit);
    }

```
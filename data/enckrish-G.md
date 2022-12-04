

| | Issue| Instances |
|--|--|--|
| GAS-1 | Reorganize nonce read/write in `relayCalls` | 3 |
| GAS-2 | Loop counter can be incremented in `unchecked` block | 1 |


## [GAS-1] Reorganize nonce read/write in `relayCalls`
Currently, in `relayCalls` function of the relayer contracts, the nonces are incremented and read this way:
```solidity
nonce++;

uint256 _nonce = nonce;
```
It can be changed to this to save gas:
```solidity
uint256 _nonce = ++nonce;
```

The execution gas savings is as follows:
| contract | avg | median | max | calls |
| -------- | -------- | -------- | --| -|
| CrossChainRelayerArbitrum | 66     | 100     | 100     |3 |
| CrossChainRelayerOptimism | 46     | 46     | 91     |2 |
| CrossChainRelayerPolygon | 48     | 48     | 97     | 2|

Average savings: 55

## [GAS-2] Loop counter can be incremented in `unchecked` block
In `CallLib.executeCalls`, by moving`_callIndex` increment to a unchecked block, we can save some gas on each iteration. 
```solidity
unchecked {
    ++_callIndex;
}
```
The following gas changes are observed in `executeCalls` (for Optimism and Arbitrium) and `processMessageFromRoot` (for Polygon)
| contract | avg | median | max | calls |
| -------- | -------- | -------- | --| -|
| *Across* CrossChainRelayer*s* | 32     | 32     | 63     |8 |


Average savings: 32




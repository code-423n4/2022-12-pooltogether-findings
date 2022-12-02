# USE UNCHECKED BLOCK FOR NONCE INCREMENT (3 INSTANCES)

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol: [78](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78)
src/ethereum-optimism/EthereumToOptimismRelayer.sol: [60](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60)
src/ethereum-polygon/EthereumToPolygonRelayer.sol: [56](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56)

## Suggest

change ```nonce++``` to
```
unchecked {
 ++nonce;
}
```

## Gas reports before change 

|EthereumToArbitrumRelayer.sol |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 487494                                                                                 | 2846            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| relayCalls                                                                             | 561             | 34385 | 51298  | 51298 | 3       |

| EthereumToOptimismRelayer.sol |                 |       |        |        |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |        |         |
| 305313                                                                                 | 1935            |       |        |        |         |
| Function Name                                                                          | min             | avg   | median | max    | # calls |
| relayCalls                                                                             | 490             | 67542 | 67542  | 134594 | 2       |

| EthereumToPolygonRelayer.sol |                 |       |        |       |         |
|-------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                     | Deployment Size |       |        |       |         |
| 1668015                                                                             | 8526            |       |        |       |         |
| Function Name                                                                       | min             | avg   | median | max   | # calls |
| relayCalls                                                                          | 547             | 29545 | 29545  | 58544 | 2       |



## Gas reports after change

| EthereumToArbitrumRelayer.sol |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 477081                                                                                 | 2794            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| relayCalls                                                                             | 561             | 34280 | 51140  | 51140 | 3       |

| EthereumToOptimismRelayer.sol |                 |       |        |        |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |        |         |
| 294701                                                                                 | 1882            |       |        |        |         |
| Function Name                                                                          | min             | avg   | median | max    | # calls |
| relayCalls                                                                             | 490             | 67458 | 67458  | 134426 | 2       |

| EthereumToPolygonRelayer.sol |                 |       |        |       |         |
|-------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                     | Deployment Size |       |        |       |         |
| 1665408                                                                             | 8513            |       |        |       |         |
| Function Name                                                                       | min             | avg   | median | max   | # calls |
| relayCalls                                                                          | 547             | 29461 | 29461  | 58376 | 2       |

# USE UNCHECKED BLOCK WHEN INCREMENT IN FOR LOOP (1 INSTANCE)

src/libraries/CallLib.so: [61](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61)

## Suggest

```code
for (uint256 _callIndex; _callIndex < _callsLength;) {
      Call memory _call = _calls[_callIndex];

      (bool _success, bytes memory _returnData) = _call.target.call(
        abi.encodePacked(_call.data, _nonce, _sender)
      );

      if (!_success) {
        revert CallFailure(_callIndex, _returnData);
      }

      unchecked {
        _callIndex++;
      }
}
```

## Gas reports before change

| EthereumToArbitrumExecutor.sol|                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 357193                                                                                   | 1816            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 820             | 20017 | 20350  | 38549 | 4       |

| EthereumToOptimismExecutor.sol |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 401508                                                                                   | 2271            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 837             | 16539 | 16539  | 32241 | 2       |

| EthereumToPolygonExecutor.sol |                 |       |        |       |         |
|---------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                       | Deployment Size |       |        |       |         |
| 447010                                                                                | 2300            |       |        |       |         |
| Function Name                                                                         | min             | avg   | median | max   | # calls |
| processMessageFromRoot                                                                | 26868           | 29573 | 29573  | 32279 | 2       |

## Gas reports after change

| EthereumToArbitrumExecutor.sol |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 347186                                                                                   | 1766            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 820             | 19981 | 20314  | 38478 | 4       |

| EthereumToOptimismExecutor.sol |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 391502                                                                                   | 2221            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 837             | 16503 | 16503  | 32170 | 2       |

| EthereumToPolygonExecutor.sol |                 |       |        |       |         |
|---------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                       | Deployment Size |       |        |       |         |
| 436998                                                                                | 2250            |       |        |       |         |
| Function Name                                                                         | min             | avg   | median | max   | # calls |
| processMessageFromRoot                                                                | 26868           | 29538 | 29538  | 32208 | 2       |







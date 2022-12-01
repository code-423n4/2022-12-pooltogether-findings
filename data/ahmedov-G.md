# USE UNCHECKED BLOCK FOR NONCE INCREMENT (3 INSTANCES)

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol: [78](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78)
src/ethereum-optimism/EthereumToOptimismRelayer.sol: [60](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60)
src/ethereum-polygon/EthereumToPolygonRelayer.sol: [56](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56)

## Gas reports before change 

|EthereumToArbitrumRelayer.sol |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 487494                                                                                 | 2846            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| relayCalls                                                                             | 561             | 34385 | 51298  | 51298 | 3       |

## Gas reports after change

| EthereumToArbitrumRelayer.sol |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 477081                                                                                 | 2794            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| relayCalls                                                                             | 561             | 34280 | 51140  | 51140 | 3       |

## Suggest

change ```nonce++``` to
```
unchecked {
 ++nonce;
}
```
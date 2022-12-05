## Table of Contents
- Should Use Unchecked Block where Over/Underflow is not Possible
- Internal Function Called Only Once can be Inlined

&ensp;
### Should Use Unchecked Block where Over/Underflow is not Possible

#### Issue
Since Solidity 0.8.0, all arithmetic operations revert on overflow and underflow by default.
However in places where overflow and underflow is not possible, it is better to use unchecked block to reduce the gas usage.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#checked-or-unchecked-arithmetic

#### PoC
1. Increments at CallLib.sol (line 61) can be unchecked 
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61
Change to
```solidity
    for (uint256 _callIndex; _callIndex < _callsLength;) {
      Call memory _call = _calls[_callIndex];

      (bool _success, bytes memory _returnData) = _call.target.call(
        abi.encodePacked(_call.data, _nonce, _sender)
      );

      if (!_success) {
        revert CallFailure(_callIndex, _returnData);
      }
      unchecked { _callIndex++; }
    }
```

#### `Gas Amount Saved`
1. EthereumToArbitrumExecutor.sol
Deployment Cost : 10,007
executeCalls avg:     44
2. EthereumToOptimismExecutor.sol
Deployment Cost : 10,006
executeCalls avg:     36
3. EthereumToPolygonExecutor.sol
Deployment Cost           : 10,012
processMessageFromRoot avg:     35

#### `Before Change`
| src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 357193                                                                                   | 1816            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 820             | 20017 | 20350  | 38549 | 4       |

| src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 401508                                                                                   | 2271            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 837             | 16539 | 16539  | 32241 | 2       |

| src/ethereum-polygon/EthereumToPolygonExecutor.sol:CrossChainExecutorPolygon contract |                 |       |        |       |         |
|---------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                       | Deployment Size |       |        |       |         |
| 447010                                                                                | 2300            |       |        |       |         |
| Function Name                                                                         | min             | avg   | median | max   | # calls |
| processMessageFromRoot                                                                | 26868           | 29573 | 29573  | 32279 | 2       |

#### `After Change`
| src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 347186                                                                                   | 1766            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 820             | 19973 | 20303  | 38467 | 4       |

| src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 391502                                                                                   | 2221            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 837             | 16503 | 16503  | 32170 | 2       |

| src/ethereum-polygon/EthereumToPolygonExecutor.sol:CrossChainExecutorPolygon contract |                 |       |        |       |         |
|---------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                       | Deployment Size |       |        |       |         |
| 436998                                                                                | 2250            |       |        |       |         |
| Function Name                                                                         | min             | avg   | median | max   | # calls |
| processMessageFromRoot                                                                | 26868           | 29538 | 29538  | 32208 | 2       |

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 2 instances found.

1. _isAuthorized() of EthereumToOptimismExecutor.sol
This function called only once at line 51 (executeCalls function)
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L77
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L51

#### `Gas Amount Saved`
Deployment Cost : 3200
executeCalls avg:   27 

#### `Before Change`
| src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 401508                                                                                   | 2271            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| crossDomainMessenger                                                                     | 271             | 271   | 271    | 271   | 1       |
| executeCalls                                                                             | 837             | 16539 | 16539  | 32241 | 2       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
| setRelayer                                                                               | 22581           | 22581 | 22581  | 22581 | 6       |

#### `After Change`
| src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 398308                                                                                   | 2255            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| crossDomainMessenger                                                                     | 271             | 271   | 271    | 271   | 1       |
| executeCalls                                                                             | 816             | 16512 | 16512  | 32209 | 2       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
| setRelayer                                                                               | 22581           | 22581 | 22581  | 22581 | 6       |


2. _isAuthorized() of EthereumToArbitrumExecutor.sol
This function called only once at line 37 (executeCalls function)
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L64
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L37

#### `Gas Amount Saved`
executeCalls avg: 8

#### `Before Change`
| src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 357193                                                                                   | 1816            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 820             | 20017 | 20350  | 38549 | 4       |
| executed                                                                                 | 494             | 494   | 494    | 494   | 1       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
| setRelayer                                                                               | 508             | 18902 | 22581  | 22581 | 6       |

#### `After Change`
| src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 357193                                                                                   | 1816            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 820             | 20009 | 20339  | 38538 | 4       |
| executed                                                                                 | 494             | 494   | 494    | 494   | 1       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
| setRelayer                                                                               | 508             | 18902 | 22581  | 22581 | 6       |


#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
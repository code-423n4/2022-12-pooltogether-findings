# change onlyOwner / admin / external functions not for endusers to payable
If a function is only callable by special users like admins it makes sense to add payable. The compiler removes checks for msg.value, saving approximately 20 gas per function call.

```solidity
File: src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol
52:   function setRelayer(ICrossChainRelayer _relayer) external { // @audit use payable to save gas

File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
139:   function setExecutor(ICrossChainExecutor _executor) external { // @audit use payable to save gas

File: src/ethereum-optimism/EthereumToOptimismExecutor.sol
66:   function setRelayer(ICrossChainRelayer _relayer) external { // @audit use payable to save gas

File: src/ethereum-optimism/EthereumToOptimismRelayer.sol
85:   function setExecutor(ICrossChainExecutor _executor) external { // @audit use payable to save gas
```

Diff
```git
 | src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
 |------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                          | Deployment Size |       |        |       |         |
-| 357193                                                                                   | 1816            |       |        |       |         |
+| 362399                                                                                   | 1842            |       |        |       |         |
 | Function Name                                                                            | min             | avg   | median | max   | # calls |
 | executeCalls                                                                             | 820             | 20017 | 20350  | 38549 | 4       |
 | executed                                                                                 | 494             | 494   | 494    | 494   | 1       |
 | relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
-| setRelayer                                                                               | 508             | 18902 | 22581  | 22581 | 6       |
+| setRelayer                                                                               | 484             | 18878 | 22557  | 22557 | 6       |

 | src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:CrossChainRelayerArbitrum contract |                 |       |        |       |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |       |         |
-| 487494                                                                                 | 2846            |       |        |       |         |
+| 484888                                                                                 | 2833            |       |        |       |         |
 | Function Name                                                                          | min             | avg   | median | max   | # calls |
 | executor                                                                               | 337             | 337   | 337    | 337   | 1       |
 | getTxHash                                                                              | 1786            | 1786  | 1786   | 1786  | 4       |
@@ -21,29 +21,29 @@
 | processCalls                                                                           | 4034            | 6039  | 6039   | 8044  | 2       |
 | relayCalls                                                                             | 561             | 34385 | 51298  | 51298 | 3       |
 | relayed                                                                                | 495             | 1495  | 1495   | 2495  | 2       |
-| setExecutor                                                                            | 564             | 19474 | 22626  | 22626 | 7       |
+| setExecutor                                                                            | 540             | 19450 | 22602  | 22602 | 7       |

 | src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
 |------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                          | Deployment Size |       |        |       |         |
-| 401508                                                                                   | 2271            |       |        |       |         |
+| 409315                                                                                   | 2311            |       |        |       |         |
 | Function Name                                                                            | min             | avg   | median | max   | # calls |
 | crossDomainMessenger                                                                     | 271             | 271   | 271    | 271   | 1       |
 | executeCalls                                                                             | 837             | 16539 | 16539  | 32241 | 2       |
 | relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
-| setRelayer                                                                               | 22581           | 22581 | 22581  | 22581 | 6       |
+| setRelayer                                                                               | 22557           | 22557 | 22557  | 22557 | 6       |

 | src/ethereum-optimism/EthereumToOptimismRelayer.sol:CrossChainRelayerOptimism contract |                 |       |        |        |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |        |         |
-| 305313                                                                                 | 1935            |       |        |        |         |
+| 302713                                                                                 | 1922            |       |        |        |         |
 | Function Name                                                                          | min             | avg   | median | max    | # calls |
 | crossDomainMessenger                                                                   | 282             | 282   | 282    | 282    | 1       |
 | executor                                                                               | 381             | 381   | 381    | 381    | 1       |
 | relayCalls                                                                             | 490             | 67542 | 67542  | 134594 | 2       |
-| setExecutor                                                                            | 22603           | 22603 | 22603  | 22603  | 6       |
+| setExecutor                                                                            | 22579           | 22579 | 22579  | 22579  | 6       |
```

# splitting require() statements that use &&
Instead of using the && operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 8 GAS per &&

```solidity
File: src/ethereum-optimism/EthereumToOptimismExecutor.sol
80:     require( // @audit splitting require() statements that use &&
81:       msg.sender == address(_crossDomainMessenger) &&
82:         _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
83:       "Executor/sender-unauthorized"
84:     );
```

Diff
```git
@@ -27,10 +27,10 @@
 | src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
 |------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                          | Deployment Size |       |        |       |         |
-| 401508                                                                                   | 2271            |       |        |       |         |
+| 415121                                                                                   | 2339            |       |        |       |         |
 | Function Name                                                                            | min             | avg   | median | max   | # calls |
 | crossDomainMessenger                                                                     | 271             | 271   | 271    | 271   | 1       |
-| executeCalls                                                                             | 837             | 16539 | 16539  | 32241 | 2       |
+| executeCalls                                                                             | 817             | 16525 | 16525  | 32233 | 2       |
 | relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
 | setRelayer                                                                               | 22581           | 22581 | 22581  | 22581 | 6       |
```

# use unchecked if no over- / underflow is possible
If it's not possible that the equation will over- / underflow, especially if it was checked previously it's better to use an unchecked{} block to save gas.

```solidity
File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
78:     nonce++; // @audit go for unchecke{} as nonce is only incremented here once per transaction and it's impossible to overflow

File: src/ethereum-optimism/EthereumToOptimismRelayer.sol
60:     nonce++; // @audit go for unchecke{} as nonce is only incremented here once per transaction and it's impossible to overflow

File: src/ethereum-polygon/EthereumToPolygonRelayer.sol
56:     nonce++; // @audit go for unchecke{} as nonce is only incremented here once per transaction and it's impossible to overflow

File: src/libraries/CallLib.sol
61:     for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) { // @audit go for unchecke{} to count up _callIndex
```

Diff
```git
@@ -1,9 +1,9 @@
 | src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
 |------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                          | Deployment Size |       |        |       |         |
-| 357193                                                                                   | 1816            |       |        |       |         |
+| 347186                                                                                   | 1766            |       |        |       |         |
 | Function Name                                                                            | min             | avg   | median | max   | # calls |
-| executeCalls                                                                             | 820             | 20017 | 20350  | 38549 | 4       |
+| executeCalls                                                                             | 820             | 19981 | 20314  | 38478 | 4       |
 | executed                                                                                 | 494             | 494   | 494    | 494   | 1       |
 | relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
 | setRelayer                                                                               | 508             | 18902 | 22581  | 22581 | 6       |
@@ -12,14 +12,14 @@
 | src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:CrossChainRelayerArbitrum contract |                 |       |        |       |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |       |         |
-| 487494                                                                                 | 2846            |       |        |       |         |
+| 477081                                                                                 | 2794            |       |        |       |         |
 | Function Name                                                                          | min             | avg   | median | max   | # calls |
 | executor                                                                               | 337             | 337   | 337    | 337   | 1       |
-| getTxHash                                                                              | 1786            | 1786  | 1786   | 1786  | 4       |
+| getTxHash                                                                              | 1794            | 1794  | 1794   | 1794  | 4       |
 | inbox                                                                                  | 282             | 282   | 282    | 282   | 1       |
 | maxGasLimit                                                                            | 251             | 251   | 251    | 251   | 1       |
-| processCalls                                                                           | 4034            | 6039  | 6039   | 8044  | 2       |
-| relayCalls                                                                             | 561             | 34385 | 51298  | 51298 | 3       |
+| processCalls                                                                           | 4042            | 6051  | 6051   | 8060  | 2       |
+| relayCalls                                                                             | 561             | 34280 | 51140  | 51140 | 3       |
 | relayed                                                                                | 495             | 1495  | 1495   | 2495  | 2       |
 | setExecutor                                                                            | 564             | 19474 | 22626  | 22626 | 7       |
@@ -27,10 +27,10 @@
 | src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
 |------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                          | Deployment Size |       |        |       |         |
-| 401508                                                                                   | 2271            |       |        |       |         |
+| 391502                                                                                   | 2221            |       |        |       |         |
 | Function Name                                                                            | min             | avg   | median | max   | # calls |
 | crossDomainMessenger                                                                     | 271             | 271   | 271    | 271   | 1       |
-| executeCalls                                                                             | 837             | 16539 | 16539  | 32241 | 2       |
+| executeCalls                                                                             | 837             | 16503 | 16503  | 32170 | 2       |
 | relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
 | setRelayer                                                                               | 22581           | 22581 | 22581  | 22581 | 6       |

@@ -38,35 +38,35 @@
 | src/ethereum-optimism/EthereumToOptimismRelayer.sol:CrossChainRelayerOptimism contract |                 |       |        |        |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |        |         |
-| 305313                                                                                 | 1935            |       |        |        |         |
+| 294701                                                                                 | 1882            |       |        |        |         |
 | Function Name                                                                          | min             | avg   | median | max    | # calls |
 | crossDomainMessenger                                                                   | 282             | 282   | 282    | 282    | 1       |
 | executor                                                                               | 381             | 381   | 381    | 381    | 1       |
-| relayCalls                                                                             | 490             | 67542 | 67542  | 134594 | 2       |
+| relayCalls                                                                             | 490             | 67458 | 67458  | 134426 | 2       |
 | setExecutor                                                                            | 22603           | 22603 | 22603  | 22603  | 6       |


 | src/ethereum-polygon/EthereumToPolygonExecutor.sol:CrossChainExecutorPolygon contract |                 |       |        |       |         |
 |---------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                       | Deployment Size |       |        |       |         |
-| 447010                                                                                | 2300            |       |        |       |         |
+| 436998                                                                                | 2250            |       |        |       |         |
 | Function Name                                                                         | min             | avg   | median | max   | # calls |
 | fxChild                                                                               | 304             | 304   | 304    | 304   | 1       |
 | fxRootTunnel                                                                          | 326             | 326   | 326    | 326   | 1       |
-| processMessageFromRoot                                                                | 26868           | 29573 | 29573  | 32279 | 2       |
+| processMessageFromRoot                                                                | 26868           | 29538 | 29538  | 32208 | 2       |
 | setFxRootTunnel                                                                       | 22625           | 22625 | 22625  | 22625 | 3       |


 | src/ethereum-polygon/EthereumToPolygonRelayer.sol:CrossChainRelayerPolygon contract |                 |       |        |       |         |
 |-------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                     | Deployment Size |       |        |       |         |
-| 1668015                                                                             | 8526            |       |        |       |         |
+| 1665408                                                                             | 8513            |       |        |       |         |
 | Function Name                                                                       | min             | avg   | median | max   | # calls |
 | checkpointManager                                                                   | 381             | 381   | 381    | 381   | 1       |
 | fxChildTunnel                                                                       | 337             | 337   | 337    | 337   | 1       |
 | fxRoot                                                                              | 403             | 403   | 403    | 403   | 1       |
 | maxGasLimit                                                                         | 229             | 229   | 229    | 229   | 1       |
-| relayCalls                                                                          | 547             | 29545 | 29545  | 58544 | 2       |
+| relayCalls                                                                          | 547             | 29461 | 29461  | 58376 | 2       |
 | setFxChildTunnel                                                                    | 22625           | 22625 | 22625  | 22625 | 3       |


@@ -92,6 +92,6 @@
 | test/fork/EthereumToPolygonFork.t.sol:EthereumToPolygonForkTest contract |                 |     |        |     |         |
 |--------------------------------------------------------------------------|-----------------|-----|--------|-----|---------|
 | Deployment Cost                                                          | Deployment Size |     |        |     |         |
-| 6572162                                                                  | 30325           |     |        |     |         |
+| 6559544                                                                  | 30262           |     |        |     |         |
 | Function Name                                                            | min             | avg | median | max | # calls |
 | setGreeting                                                              | 236             | 236 | 236    | 236 | 1       |
```

# define named return variable to save gas
instead of defining a local variable and returning it, you can save gas by defining the name in the returns definition.

```solidity
File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
108:   ) external payable returns (uint256 _ticketID) { // @audit define _ticketID as return var to save gas
```

Diff
```git
@@ -12,13 +12,13 @@
 | src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:CrossChainRelayerArbitrum contract |                 |       |        |       |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |       |         |
-| 487494                                                                                 | 2846            |       |        |       |         |
+| 487288                                                                                 | 2845            |       |        |       |         |
 | Function Name                                                                          | min             | avg   | median | max   | # calls |
 | executor                                                                               | 337             | 337   | 337    | 337   | 1       |
 | getTxHash                                                                              | 1786            | 1786  | 1786   | 1786  | 4       |
 | inbox                                                                                  | 282             | 282   | 282    | 282   | 1       |
 | maxGasLimit                                                                            | 251             | 251   | 251    | 251   | 1       |
-| processCalls                                                                           | 4034            | 6039  | 6039   | 8044  | 2       |
+| processCalls                                                                           | 4034            | 6038  | 6038   | 8042  | 2       |
```

# optimize state variable access
Try to avoide accessing state variables multiple times per function.  
For the nonce access, you can just use "uint256 _nonce = ++nonce;" - this will count up the state variable, and save gas.
```solidity
File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
78:     //nonce++;
79: 
80:     uint256 _nonce = ++nonce; // @audit dont access nonce multiple times

File: src/ethereum-optimism/EthereumToOptimismRelayer.sol
60:     //nonce++;
61: 
62:     uint256 _nonce = ++nonce; // @audit dont access nonce multiple times

File: src/ethereum-polygon/EthereumToPolygonRelayer.sol
56:     //nonce++;
57: 
58:     uint256 _nonce = ++nonce; // @audit dont access nonce multiple times

```

Diff
```git
@@ -19,7 +19,7 @@
 | inbox                                                                                  | 282             | 282   | 282    | 282   | 1       |
 | maxGasLimit                                                                            | 251             | 251   | 251    | 251   | 1       |
 | processCalls                                                                           | 4034            | 6039  | 6039   | 8044  | 2       |
-| relayCalls                                                                             | 561             | 34385 | 51298  | 51298 | 3       |
+| relayCalls                                                                             | 561             | 34319 | 51198  | 51198 | 3       |
 | relayed                                                                                | 495             | 1495  | 1495   | 2495  | 2       |
 | setExecutor                                                                            | 564             | 19474 | 22626  | 22626 | 7       |

@@ -38,11 +38,11 @@
 | src/ethereum-optimism/EthereumToOptimismRelayer.sol:CrossChainRelayerOptimism contract |                 |       |        |        |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |        |         |
-| 305313                                                                                 | 1935            |       |        |        |         |
+| 305713                                                                                 | 1937            |       |        |        |         |
 | Function Name                                                                          | min             | avg   | median | max    | # calls |
 | crossDomainMessenger                                                                   | 282             | 282   | 282    | 282    | 1       |
 | executor                                                                               | 381             | 381   | 381    | 381    | 1       |
-| relayCalls                                                                             | 490             | 67542 | 67542  | 134594 | 2       |
+| relayCalls                                                                             | 490             | 67496 | 67496  | 134503 | 2       |
 | setExecutor                                                                            | 22603           | 22603 | 22603  | 22603  | 6       |


@@ -66,7 +66,7 @@
 | fxChildTunnel                                                                       | 337             | 337   | 337    | 337   | 1       |
 | fxRoot                                                                              | 403             | 403   | 403    | 403   | 1       |
 | maxGasLimit                                                                         | 229             | 229   | 229    | 229   | 1       |
-| relayCalls                                                                          | 547             | 29545 | 29545  | 58544 | 2       |
+| relayCalls                                                                          | 547             | 29497 | 29497  | 58447 | 2       |
 | setFxChildTunnel                                                                    | 22625           | 22625 | 22625  | 22625 | 3       |
```

# constants can be private
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

```solidity
File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
31:   IInbox public immutable inbox; // @audit constants can be private

File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
37:   uint256 public immutable maxGasLimit; // @audit constants can be private

File: src/ethereum-optimism/EthereumToOptimismExecutor.sol
19:   ICrossDomainMessenger public immutable crossDomainMessenger;  // @audit constants can be private

File: src/ethereum-optimism/EthereumToOptimismRelayer.sol
20:   ICrossDomainMessenger public immutable crossDomainMessenger;  // @audit constants can be private

File: src/ethereum-optimism/EthereumToOptimismRelayer.sol
26:   uint256 public immutable maxGasLimit;  // @audit constants can be private

File: src/ethereum-polygon/EthereumToPolygonRelayer.sol
20:   uint256 public immutable maxGasLimit;  // @audit constants can be private
```

Diff
```git
 | src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:CrossChainRelayerArbitrum contract |                 |       |        |       |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |       |         |
-| 487494                                                                                 | 2846            |       |        |       |         |
+| 459633                                                                                 | 2693            |       |        |       |         |

 | src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
 |------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                          | Deployment Size |       |        |       |         |
-| 401508                                                                                   | 2271            |       |        |       |         |
+| 391487                                                                                   | 2215            |       |        |       |         |

 | src/ethereum-optimism/EthereumToOptimismRelayer.sol:CrossChainRelayerOptimism contract |                 |       |        |        |         |
 |----------------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                                                        | Deployment Size |       |        |        |         |
-| 305313                                                                                 | 1935            |       |        |        |         |
+| 279858                                                                                 | 1794            |       |        |        |         |

 | src/ethereum-polygon/EthereumToPolygonRelayer.sol:CrossChainRelayerPolygon contract |                 |       |        |       |         |
 |-------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                     | Deployment Size |       |        |       |         |
-| 1668015                                                                             | 8526            |       |        |       |         |
+| 1655386                                                                             | 8457            |       |        |       |         |

 | test/fork/EthereumToPolygonFork.t.sol:EthereumToPolygonForkTest contract |                 |     |        |     |         |
 |--------------------------------------------------------------------------|-----------------|-----|--------|-----|---------|
 | Deployment Cost                                                          | Deployment Size |     |        |     |         |
-| 6572162                                                                  | 30325           |     |        |     |         |
+| 6533509                                                                  | 30132           |     |        |     |         |
```
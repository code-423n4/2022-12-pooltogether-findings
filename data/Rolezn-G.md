## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 1 | 35 |
| [GAS&#x2011;2](#GAS&#x2011;2) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 4 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | Optimize names to save gas | 6 | 132 |
| [GAS&#x2011;4](#GAS&#x2011;4) | `internal` functions only called once can be inlined to save gas | 3 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | Setting the `constructor` to `payable` | 5 | 65 |
| [GAS&#x2011;6](#GAS&#x2011;6) | `relayer` can be set as immutable and set in `constructor` to save gas | 4 | 8000 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Repeat `require` can be set as a function to save gas | 4 | - |


Total: 27 contexts over 7 issues

## Gas Optimizations



### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
61: for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61





### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```solidity
53: require(address(relayer) == address(0), "Executor/relayer-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L53

```solidity
140: require(address(executor) == address(0), "Relayer/executor-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L140

```solidity
67: require(address(relayer) == address(0), "Executor/relayer-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L67

```solidity
86: require(address(executor) == address(0), "Relayer/executor-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L86





### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

```solidity
File: \ERC5164\src\ethereum-arbitrum\EthereumToArbitrumExecutor.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol

```solidity
File: \ERC5164\src\ethereum-arbitrum\EthereumToArbitrumRelayer.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

```solidity
File: \ERC5164\src\ethereum-optimism\EthereumToOptimismExecutor.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol

```solidity
File: \ERC5164\src\ethereum-optimism\EthereumToOptimismRelayer.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol

```solidity
File: \ERC5164\src\ethereum-polygon\EthereumToPolygonExecutor.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol

```solidity
File: \ERC5164\src\ethereum-polygon\EthereumToPolygonRelayer.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol



#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.




### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
64: function _isAuthorized

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L64

```solidity
77: function _isAuthorized

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L77





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Setting the `constructor` to `payable`

Saves ~15 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
56: constructor(IInbox _inbox, uint256 _maxGasLimit)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L56

```solidity
37: constructor(ICrossDomainMessenger _crossDomainMessenger)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L37

```solidity
38: constructor(ICrossDomainMessenger _crossDomainMessenger, uint256 _maxGasLimit)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L38

```solidity
39: constructor(address _fxChild) FxBaseChildTunnel(_fxChild)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L39

```solidity
33: constructor(
    address _checkpointManager,
    address _fxRoot,
    uint256 _maxGasLimit
  ) FxBaseRootTunnel(_checkpointManager, _fxRoot)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L33


### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> `relayer` can be set as immutable and set in `constructor` to save gas

Current implementation of the relayer sets its value via the `setRelayer` function.
The value of the `relayer` can be set in the constructor and also set it as an `immutable` since the value of `relayer` cannot change once it has been set due to the `require(address(relayer) == address(0), "Executor/relayer-already-set");` statement.

The same idea applies for `setExecutor` function

#### <ins>Proof Of Concept</ins>

```solidity
ICrossChainRelayer public relayer;
...
  function setRelayer(ICrossChainRelayer _relayer) external {
    require(address(relayer) == address(0), "Executor/relayer-already-set");
    relayer = _relayer;
  }
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol

#### <ins>Recommended Mitigation Steps</ins>

Set `relayer` as immutable:

```solidity
ICrossChainRelayer public immutable relayer;
```

And set the relayer in the constructor:

```solidity
constructor(ICrossDomainMessenger _crossDomainMessenger, ICrossChainRelayer _relayer) {
    require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");
    crossDomainMessenger = _crossDomainMessenger;
    require(address(relayer) == address(0), "Executor/relayer-already-set");
    relayer = _relayer;
  }
```

### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Repeat `require` can be set as a function to save gas

The following `require` statement repeat and can be set as a function the `CallLib.sol` since they import the library already.

#### <ins>Proof Of Concept</ins>

```solidity
53: require(address(relayer) == address(0), "Executor/relayer-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L53

```solidity
140: require(address(executor) == address(0), "Relayer/executor-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L140

```solidity
67: require(address(relayer) == address(0), "Executor/relayer-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L67

```solidity
86: require(address(executor) == address(0), "Relayer/executor-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L86


#### <ins>Recommended Mitigation Steps</ins>

Add a function for the repeat `require` statements in the `CallLib.sol` library and call it instead.

```solidity
function checkAddressZero(address _address) {
  require(address(_address) == address(0), "Executor/relayer-already-set")
}
```


#### Results comparison

Original:

```
| src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 357193                                                                                   | 1816            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 820             | 20017 | 20350  | 38549 | 4       |
| executed                                                                                 | 494             | 494   | 494    | 494   | 1       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
| setRelayer                                                                               | 508             | 18902 | 22581  | 22581 | 6       |


| src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:CrossChainRelayerArbitrum contract |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 487494                                                                                 | 2846            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| executor                                                                               | 337             | 337   | 337    | 337   | 1       |
| getTxHash                                                                              | 1786            | 1786  | 1786   | 1786  | 4       |
| inbox                                                                                  | 282             | 282   | 282    | 282   | 1       |
| maxGasLimit                                                                            | 251             | 251   | 251    | 251   | 1       |
| processCalls                                                                           | 4034            | 6039  | 6039   | 8044  | 2       |
| relayCalls                                                                             | 561             | 34385 | 51298  | 51298 | 3       |
| relayed                                                                                | 495             | 1495  | 1495   | 2495  | 2       |
| setExecutor                                                                            | 564             | 19474 | 22626  | 22626 | 7       |


| src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 401508                                                                                   | 2271            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| crossDomainMessenger                                                                     | 271             | 271   | 271    | 271   | 1       |
| executeCalls                                                                             | 837             | 837   | 837    | 837   | 1       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
| setRelayer                                                                               | 22581           | 22581 | 22581  | 22581 | 6       |


| src/ethereum-optimism/EthereumToOptimismRelayer.sol:CrossChainRelayerOptimism contract |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 305313                                                                                 | 1935            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| crossDomainMessenger                                                                   | 282             | 282   | 282    | 282   | 1       |
| executor                                                                               | 381             | 381   | 381    | 381   | 1       |
| relayCalls                                                                             | 490             | 14141 | 14141  | 27792 | 2       |
| setExecutor                                                                            | 22603           | 22603 | 22603  | 22603 | 6       |


| src/ethereum-polygon/EthereumToPolygonExecutor.sol:CrossChainExecutorPolygon contract |                 |       |        |       |         |
|---------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                       | Deployment Size |       |        |       |         |
| 447010                                                                                | 2300            |       |        |       |         |
| Function Name                                                                         | min             | avg   | median | max   | # calls |
| fxChild                                                                               | 304             | 304   | 304    | 304   | 1       |
| fxRootTunnel                                                                          | 326             | 326   | 326    | 326   | 1       |
| processMessageFromRoot                                                                | 26868           | 29573 | 29573  | 32279 | 2       |
| setFxRootTunnel                                                                       | 22625           | 22625 | 22625  | 22625 | 3       |


| src/ethereum-polygon/EthereumToPolygonRelayer.sol:CrossChainRelayerPolygon contract |                 |       |        |       |         |
|-------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                     | Deployment Size |       |        |       |         |
| 1668015                                                                             | 8526            |       |        |       |         |
| Function Name                                                                       | min             | avg   | median | max   | # calls |
| checkpointManager                                                                   | 381             | 381   | 381    | 381   | 1       |
| fxChildTunnel                                                                       | 337             | 337   | 337    | 337   | 1       |
| fxRoot                                                                              | 403             | 403   | 403    | 403   | 1       |
| maxGasLimit                                                                         | 229             | 229   | 229    | 229   | 1       |
| relayCalls                                                                          | 547             | 15110 | 15110  | 29674 | 2       |
| setFxChildTunnel                                                                    | 22625           | 22625 | 22625  | 22625 | 3       |


| test/contracts/Greeter.sol:Greeter contract |                 |      |        |      |         |
|---------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                             | Deployment Size |      |        |      |         |
| 343595                                      | 2513            |      |        |      |         |
| Function Name                               | min             | avg  | median | max  | # calls |
| greet                                       | 1261            | 1261 | 1261   | 1261 | 3       |
| greeting                                    | 1275            | 1275 | 1275   | 1275 | 2       |
| setGreeting                                 | 737             | 4724 | 4182   | 8982 | 5       |


| test/contracts/mock/ArbInbox.sol:ArbInbox contract |                 |      |        |      |         |
|----------------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                                    | Deployment Size |      |        |      |         |
| 522756                                             | 2643            |      |        |      |         |
| Function Name                                      | min             | avg  | median | max  | # calls |
| createRetryableTicket                              | 1169            | 1169 | 1169   | 1169 | 1       |
| generateRandomNumber                               | 443             | 443  | 443    | 443  | 1       |


| test/fork/EthereumToPolygonFork.t.sol:EthereumToPolygonForkTest contract |                 |     |        |     |         |
|--------------------------------------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                                                          | Deployment Size |     |        |     |         |
| 6572162                                                                  | 30325           |     |        |     |         |
| Function Name                                                            | min             | avg | median | max | # calls |
| setGreeting                                                              | 236             | 236 | 236    | 236 | 1       |

```


After some of the gas optimzations (excluding GAS-6 as it requires significant changes in the test files):

```
| src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:CrossChainExecutorArbitrum contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 350986                                                                                   | 1785            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| executeCalls                                                                             | 809             | 19970 | 20303  | 38467 | 4       |
| executed                                                                                 | 494             | 494   | 494    | 494   | 1       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 2       |
| setRelayer                                                                               | 558             | 18952 | 22631  | 22631 | 6       |


| src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:CrossChainRelayerArbitrum contract |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 487494                                                                                 | 2846            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| executor                                                                               | 337             | 337   | 337    | 337   | 1       |
| getTxHash                                                                              | 1786            | 1786  | 1786   | 1786  | 4       |
| inbox                                                                                  | 282             | 282   | 282    | 282   | 1       |
| maxGasLimit                                                                            | 251             | 251   | 251    | 251   | 1       |
| processCalls                                                                           | 4034            | 6039  | 6039   | 8044  | 2       |
| relayCalls                                                                             | 561             | 34385 | 51298  | 51298 | 3       |
| relayed                                                                                | 495             | 1495  | 1495   | 2495  | 2       |
| setExecutor                                                                            | 564             | 19474 | 22626  | 22626 | 7       |


| src/ethereum-optimism/EthereumToOptimismExecutor.sol:CrossChainExecutorOptimism contract |                 |       |        |       |         |
|------------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                          | Deployment Size |       |        |       |         |
| 397899                                                                                   | 2247            |       |        |       |         |
| Function Name                                                                            | min             | avg   | median | max   | # calls |
| crossDomainMessenger                                                                     | 271             | 271   | 271    | 271   | 1       |
| executeCalls                                                                             | 799             | 799   | 799    | 799   | 1       |
| relayer                                                                                  | 326             | 326   | 326    | 326   | 1       |
| setRelayer                                                                               | 22631           | 22631 | 22631  | 22631 | 6       |


| src/ethereum-optimism/EthereumToOptimismRelayer.sol:CrossChainRelayerOptimism contract |                 |       |        |       |         |
|----------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                        | Deployment Size |       |        |       |         |
| 305313                                                                                 | 1935            |       |        |       |         |
| Function Name                                                                          | min             | avg   | median | max   | # calls |
| crossDomainMessenger                                                                   | 282             | 282   | 282    | 282   | 1       |
| executor                                                                               | 381             | 381   | 381    | 381   | 1       |
| relayCalls                                                                             | 490             | 14141 | 14141  | 27792 | 2       |
| setExecutor                                                                            | 22603           | 22603 | 22603  | 22603 | 6       |


| src/ethereum-polygon/EthereumToPolygonExecutor.sol:CrossChainExecutorPolygon contract |                 |       |        |       |         |
|---------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                       | Deployment Size |       |        |       |         |
| 436974                                                                                | 2237            |       |        |       |         |
| Function Name                                                                         | min             | avg   | median | max   | # calls |
| fxChild                                                                               | 304             | 304   | 304    | 304   | 1       |
| fxRootTunnel                                                                          | 326             | 326   | 326    | 326   | 1       |
| processMessageFromRoot                                                                | 26868           | 29538 | 29538  | 32208 | 2       |
| setFxRootTunnel                                                                       | 22625           | 22625 | 22625  | 22625 | 3       |


| src/ethereum-polygon/EthereumToPolygonRelayer.sol:CrossChainRelayerPolygon contract |                 |       |        |       |         |
|-------------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                                                                     | Deployment Size |       |        |       |         |
| 1667991                                                                             | 8512            |       |        |       |         |
| Function Name                                                                       | min             | avg   | median | max   | # calls |
| checkpointManager                                                                   | 381             | 381   | 381    | 381   | 1       |
| fxChildTunnel                                                                       | 337             | 337   | 337    | 337   | 1       |
| fxRoot                                                                              | 403             | 403   | 403    | 403   | 1       |
| maxGasLimit                                                                         | 229             | 229   | 229    | 229   | 1       |
| relayCalls                                                                          | 547             | 15110 | 15110  | 29674 | 2       |
| setFxChildTunnel                                                                    | 22625           | 22625 | 22625  | 22625 | 3       |


| test/contracts/Greeter.sol:Greeter contract |                 |      |        |      |         |
|---------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                             | Deployment Size |      |        |      |         |
| 343595                                      | 2513            |      |        |      |         |
| Function Name                               | min             | avg  | median | max  | # calls |
| greet                                       | 1261            | 1261 | 1261   | 1261 | 3       |
| greeting                                    | 1275            | 1275 | 1275   | 1275 | 2       |
| setGreeting                                 | 737             | 4724 | 4182   | 8982 | 5       |


| test/contracts/mock/ArbInbox.sol:ArbInbox contract |                 |      |        |      |         |
|----------------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                                    | Deployment Size |      |        |      |         |
| 522756                                             | 2643            |      |        |      |         |
| Function Name                                      | min             | avg  | median | max  | # calls |
| createRetryableTicket                              | 1169            | 1169 | 1169   | 1169 | 1       |
| generateRandomNumber                               | 443             | 443  | 443    | 443  | 1       |


| test/fork/EthereumToPolygonFork.t.sol:EthereumToPolygonForkTest contract |                 |     |        |     |         |
|--------------------------------------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                                                          | Deployment Size |     |        |     |         |
| 6556735                                                                  | 30248           |     |        |     |         |
| Function Name                                                            | min             | avg | median | max | # calls |
| setGreeting                                                              | 236             | 236 | 236    | 236 | 1       |
```
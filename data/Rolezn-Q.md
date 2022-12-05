## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Contract-existence Checks Before Low-level Calls | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Missing parameter validation in `constructor` | 3 |
| [LOW&#x2011;3](#LOW&#x2011;3) | There is no access control of `setRelayer` and can be called by anyone | 1 |

Total: 5 contexts over 3 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 4 |
| [NC&#x2011;2](#NC&#x2011;2) | Missing event for critical parameter change | 4 |
| [NC&#x2011;3](#NC&#x2011;3) | Implementation contract may not be initialized | 5 |
| [NC&#x2011;4](#NC&#x2011;4) | Non-usage of specific imports | 8 |
| [NC&#x2011;5](#NC&#x2011;5) | Use `bytes.concat()` | 1 |
| [NC&#x2011;6](#NC&#x2011;6) | Not all test cases pass | 6 |


Total: 28 contexts over 6 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```solidity
64: (bool _success, bytes memory _returnData) = _call.target.call(
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L64




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
39: address _fxChild

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L39

```solidity
34: address _checkpointManager
35: address _fxRoot

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L34-L35



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.

### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> There is no access control of `setRelayer` and can be called by anyone

Current implementation of `setRelayer` function allows anyone to set the relayer as there is no restriction at all to the function.

#### <ins>Proof Of Concept</ins>

```solidity
  function setRelayer(ICrossChainRelayer _relayer) external {
    require(address(relayer) == address(0), "Executor/relayer-already-set");
    relayer = _relayer;
  }
```
https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52-L55

```solidity
  function setRelayer(ICrossChainRelayer _relayer) external {
    require(address(relayer) == address(0), "Executor/relayer-already-set");
    relayer = _relayer;
  }
```
https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L66-L69

#### <ins>Recommended Mitigation Steps</ins>
Add access control to the `setRelayer` function.



## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
52: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52

```solidity
139: function setExecutor(ICrossChainExecutor _executor) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L139

```solidity
66: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L66

```solidity
85: function setExecutor(ICrossChainExecutor _executor) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L85



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
52: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52

```solidity
139: function setExecutor(ICrossChainExecutor _executor) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L139

```solidity
66: function setRelayer(ICrossChainRelayer _relayer) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L66

```solidity
85: function setExecutor(ICrossChainExecutor _executor) external {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L85





### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

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





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
7: import "../interfaces/ICrossChainExecutor.sol";
8: import "../libraries/CallLib.sol";

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L7-L8

```solidity
9: import "../libraries/CallLib.sol";

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L9

```solidity
7: import "../interfaces/ICrossChainExecutor.sol";
8: import "../libraries/CallLib.sol";

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L7-L8

```solidity
9: import "../libraries/CallLib.sol";

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L9

```solidity
7: import "../libraries/CallLib.sol";

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L7

```solidity
9: import "../libraries/CallLib.sol";

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L9




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
65: abi.encodePacked(_call.data, _nonce, _sender)

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L65




#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 


### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Not all test cases pass

It is recommended to have all test cases pass to show that indeed the required tests successfully go through their tests

#### <ins>Proof Of Concept</ins>

```
[⠢] Compiling...
No files changed, compilation skipped

Running 5 tests for test/unit/ethereum-arbitrum/EthereumToArbitrumExecutor.t.sol:CrossChainExecutorArbitrumUnitTest
[PASS] testExecuteCalls() (gas: 105030)
[PASS] testExecuteCallsAlreadyExecuted() (gas: 99358)
[PASS] testExecuteCallsUnauthorized() (gas: 54324)
[PASS] testSetRelayer() (gas: 30998)
[PASS] testSetRelayerFail() (gas: 34092)
Test result: ok. 5 passed; 0 failed; finished in 1.13ms

Running 11 tests for test/unit/ethereum-arbitrum/EthereumToArbitrumRelayer.t.sol:CrossChainRelayerArbitrumUnitTest
[PASS] testConstructor() (gas: 10676)
[PASS] testConstructorInboxFail() (gas: 43666)
[PASS] testConstructorMaxGasLimitFail() (gas: 43732)
[PASS] testGetTxHash() (gas: 31657)
[PASS] testGetTxHashFail() (gas: 33611)
[PASS] testProcessCalls() (gas: 126866)
[PASS] testProcessCallsFail() (gas: 61964)
[PASS] testRelayCalls() (gas: 116375)
[PASS] testRelayCallsFail() (gas: 65859)
[PASS] testSetExecutor() (gas: 31039)
[PASS] testSetExecutorFail() (gas: 34226)
Test result: ok. 11 passed; 0 failed; finished in 1.23ms

Running 8 tests for test/fork/EthereumToPolygonFork.t.sol:EthereumToPolygonForkTest
[PASS] testCallFailure() (gas: 2728443)
[PASS] testExecuteCalls() (gas: 2745831)
[PASS] testExecutor() (gas: 2268462)
[PASS] testGasLimitTooHigh() (gas: 2650615)
[PASS] testGreeter() (gas: 914678)
[FAIL. Reason: Log != expected log] testRelayCalls() (gas: 2686489)
[PASS] testRelayer() (gas: 2270604)
[PASS] testSetGreetingError() (gas: 2647030)
Test result: FAILED. 7 passed; 1 failed; finished in 5.69s

Running 8 tests for test/fork/EthereumToOptimismFork.t.sol:EthereumToOptimismForkTest
[FAIL. Reason: Log != expected log] testExecuteCalls() (gas: 1307024)
[PASS] testExecutor() (gas: 856681)
[PASS] testGasLimitTooHigh() (gas: 1286303)
[PASS] testGreeter() (gas: 869151)
[PASS] testIsAuthorized() (gas: 1288413)
[FAIL. Reason: Log != expected log] testRelayCalls() (gas: 1320360)
[PASS] testRelayer() (gas: 856834)
[PASS] testSetGreetingError() (gas: 1282745)
Test result: FAILED. 6 passed; 2 failed; finished in 5.90s

Failing tests:
Encountered 2 failing tests in test/fork/EthereumToOptimismFork.t.sol:EthereumToOptimismForkTest
[FAIL. Reason: Log != expected log] testExecuteCalls() (gas: 1307024)
[FAIL. Reason: Log != expected log] testRelayCalls() (gas: 1320360)

Encountered 1 failing test in test/fork/EthereumToPolygonFork.t.sol:EthereumToPolygonForkTest
[FAIL. Reason: Log != expected log] testRelayCalls() (gas: 2686489)

Encountered a total of 3 failing tests, 29 tests succeeded
```


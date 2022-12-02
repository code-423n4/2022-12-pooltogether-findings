# Gas optimizations

## Preincrement `nonce` and set in `_nonce` all using unchecked

Replace
```solidity
nonce++;
uint256 _nonce = nonce;
```
For
```solidity
// set add also `returns (uint256 _nonce)`
unchecked { _nonce = ++nonce; }
```

Gas diff;
```
testRelayCallsFail() (gas: 8 (0.012%)) 
testProcessCallsFail() (gas: 8 (0.013%)) 
testConstructorMaxGasLimitFail() (gas: -6 (-0.014%)) 
testConstructorInboxFail() (gas: -6 (-0.014%)) 
testGetTxHashFail() (gas: 8 (0.024%)) 
testGetTxHash() (gas: 8 (0.025%)) 
testExecuteCalls() (gas: -2200 (-0.080%)) 
testCallFailure() (gas: -2200 (-0.081%)) 
testGasLimitTooHigh() (gas: -2200 (-0.083%)) 
testSetGreetingError() (gas: -2200 (-0.083%)) 
testRelayCalls() (gas: -2362 (-0.087%)) 
testRelayer() (gas: -2200 (-0.097%)) 
testExecutor() (gas: -2200 (-0.097%)) 
testProcessCalls() (gas: -142 (-0.112%)) 
testRelayCalls() (gas: -150 (-0.129%)) 
testRelayCalls() (gas: -10171 (-0.713%)) 
testExecuteCalls() (gas: -10012 (-0.717%)) 
testIsAuthorized() (gas: -10012 (-0.777%)) 
testGasLimitTooHigh() (gas: -10012 (-0.778%)) 
testSetGreetingError() (gas: -10012 (-0.781%)) 
testRelayer() (gas: -10012 (-1.168%)) 
testExecutor() (gas: -10012 (-1.169%)) 
Overall gas change: -86077 (-6.905%)
```

### Suggested changes;

#### `src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol`

```diff
diff --git a/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol b/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
index f88674d..2a08ee6 100644
--- a/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
+++ b/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
@@ -67,7 +67,7 @@ contract CrossChainRelayerArbitrum is ICrossChainRelayer {
   function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
     external
     payable
-    returns (uint256)
+    returns (uint256 _nonce)
   {
     uint256 _maxGasLimit = maxGasLimit;
 
@@ -75,15 +75,11 @@ contract CrossChainRelayerArbitrum is ICrossChainRelayer {
       revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
     }
 
-    nonce++;
-
-    uint256 _nonce = nonce;
+    unchecked { _nonce = ++nonce; }
 
     relayed[_getTxHash(_nonce, _calls, msg.sender, _gasLimit)] = true;
 
     emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);
-
-    return _nonce;
   }
 
   /**
```

#### `src/ethereum-optimism/EthereumToOptimismRelayer.sol`

```diff
diff --git a/src/ethereum-optimism/EthereumToOptimismRelayer.sol b/src/ethereum-optimism/EthereumToOptimismRelayer.sol
index ac70501..d5f9fa3 100644
--- a/src/ethereum-optimism/EthereumToOptimismRelayer.sol
+++ b/src/ethereum-optimism/EthereumToOptimismRelayer.sol
@@ -49,7 +49,7 @@ contract CrossChainRelayerOptimism is ICrossChainRelayer {
   function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
     external
     payable
-    returns (uint256)
+    returns (uint256 _nonce)
   {
     uint256 _maxGasLimit = maxGasLimit;
 
@@ -57,9 +57,7 @@ contract CrossChainRelayerOptimism is ICrossChainRelayer {
       revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
     }
 
-    nonce++;
-
-    uint256 _nonce = nonce;
+    unchecked { _nonce = ++nonce; }
 
     crossDomainMessenger.sendMessage(
       address(executor),
@@ -73,8 +71,6 @@ contract CrossChainRelayerOptimism is ICrossChainRelayer {
     );
 
     emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);
-
-    return _nonce;
   }
 
   /**
```

#### `src/ethereum-polygon/EthereumToPolygonRelayer.sol`

```diff
diff --git a/src/ethereum-polygon/EthereumToPolygonRelayer.sol b/src/ethereum-polygon/EthereumToPolygonRelayer.sol
index 2867314..f9feafb 100644
--- a/src/ethereum-polygon/EthereumToPolygonRelayer.sol
+++ b/src/ethereum-polygon/EthereumToPolygonRelayer.sol
@@ -45,7 +45,7 @@ contract CrossChainRelayerPolygon is ICrossChainRelayer, FxBaseRootTunnel {
   function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
     external
     payable
-    returns (uint256)
+    returns (uint256 _nonce)
   {
     uint256 _maxGasLimit = maxGasLimit;
 
@@ -53,15 +53,11 @@ contract CrossChainRelayerPolygon is ICrossChainRelayer, FxBaseRootTunnel {
       revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
     }
 
-    nonce++;
-
-    uint256 _nonce = nonce;
+    unchecked { _nonce = ++nonce; }
 
     _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));
 
     emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);
-
-    return _nonce;
   }
 
   /* ============ Internal Functions ============ */
```


## Set `executor` as immutable

You can set the `executor` address variavle as immutable on `CrossChainRelayerOptimism` and `CrossChainRelayerArbitrum`. But you also have to change your deploy method and precalculate deployed address. Also you could remove the `setExecutor` function.

You can set the `relayer` address variavle as immutable on `CrossChainExecutorOptimism` and `CrossChainExecutorArbitrum`. But you also have to change your deploy method and precalculate deployed address. Also you could remove the `setRelayer` function.

### Suggested changes;

#### `src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol`

```diff
diff --git a/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol b/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol
index bfee411..fe6c906 100644
--- a/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol
+++ b/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol
@@ -16,7 +16,7 @@ contract CrossChainExecutorArbitrum is ICrossChainExecutor {
   /* ============ Variables ============ */
 
   /// @notice Address of the relayer contract on the Ethereum chain.
-  ICrossChainRelayer public relayer;
+  ICrossChainRelayer public immutable relayer;
 
   /**
    * @notice Nonce to uniquely identify the batch of calls that were executed.
@@ -25,6 +25,11 @@ contract CrossChainExecutorArbitrum is ICrossChainExecutor {
    */
   mapping(uint256 => bool) public executed;
 
+  constructor(ICrossChainRelayer _relayer) {
+    require(address(_relayer) != address(0), "Executor/relayer-already-set");
+    relayer = _relayer;
+  }
+
   /* ============ External Functions ============ */
 
   /// @inheritdoc ICrossChainExecutor
@@ -33,25 +38,14 @@ contract CrossChainExecutorArbitrum is ICrossChainExecutor {
     address _sender,
     CallLib.Call[] calldata _calls
   ) external {
-    ICrossChainRelayer _relayer = relayer;
-    _isAuthorized(_relayer);
+    _isAuthorized(relayer);
 
     bool _executedNonce = executed[_nonce];
     executed[_nonce] = true;
 
     CallLib.executeCalls(_nonce, _sender, _calls, _executedNonce);
 
-    emit ExecutedCalls(_relayer, _nonce);
-  }
-
-  /**
-   * @notice Set relayer contract address.
-   * @dev Will revert if it has already been set.
-   * @param _relayer Address of the relayer contract on the Ethereum chain
-   */
-  function setRelayer(ICrossChainRelayer _relayer) external {
-    require(address(relayer) == address(0), "Executor/relayer-already-set");
-    relayer = _relayer;
+    emit ExecutedCalls(relayer, _nonce);
   }
 
   /* ============ Internal Functions ============ */
```

#### `src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol`

```diff
diff --git a/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol b/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
index f88674d..80acb68 100644
--- a/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
+++ b/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
@@ -31,7 +31,7 @@ contract CrossChainRelayerArbitrum is ICrossChainRelayer {
   IInbox public immutable inbox;
 
   /// @notice Address of the executor contract on the Arbitrum chain.
-  ICrossChainExecutor public executor;
+  ICrossChainExecutor public immutable executor;
 
   /// @notice Gas limit provided for free on Arbitrum.
   uint256 public immutable maxGasLimit;
@@ -53,11 +53,13 @@ contract CrossChainRelayerArbitrum is ICrossChainRelayer {
    * @param _inbox Address of the Arbitrum inbox on Ethereum
    * @param _maxGasLimit Gas limit provided for free on Arbitrum
    */
-  constructor(IInbox _inbox, uint256 _maxGasLimit) {
+  constructor(IInbox _inbox, ICrossChainExecutor _executor, uint256 _maxGasLimit) {
     require(address(_inbox) != address(0), "Relayer/inbox-not-zero-address");
+    require(address(_executor) != address(0), "Relayer/executor-already-set");
     require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
 
     inbox = _inbox;
+    executor = _executor;
     maxGasLimit = _maxGasLimit;
   }
 
@@ -131,15 +133,6 @@ contract CrossChainRelayerArbitrum is ICrossChainRelayer {
     return _ticketID;
   }
 
-  /**
-   * @notice Set executor contract address.
-   * @dev Will revert if it has already been set.
-   * @param _executor Address of the executor contract on the Arbitrum chain
-   */
-  function setExecutor(ICrossChainExecutor _executor) external {
-    require(address(executor) == address(0), "Relayer/executor-already-set");
-    executor = _executor;
-  }
 
   /**
    * @notice Get transaction hash.
```

#### `src/ethereum-optimism/EthereumToOptimismExecutor.sol`

```diff
diff --git a/src/ethereum-optimism/EthereumToOptimismExecutor.sol b/src/ethereum-optimism/EthereumToOptimismExecutor.sol
index 1aba9c1..1988009 100644
--- a/src/ethereum-optimism/EthereumToOptimismExecutor.sol
+++ b/src/ethereum-optimism/EthereumToOptimismExecutor.sol
@@ -19,7 +19,7 @@ contract CrossChainExecutorOptimism is ICrossChainExecutor {
   ICrossDomainMessenger public immutable crossDomainMessenger;
 
   /// @notice Address of the relayer contract on the Ethereum chain.
-  ICrossChainRelayer public relayer;
+  ICrossChainRelayer public immutable relayer;
 
   /**
    * @notice Nonce to uniquely identify the batch of calls that were executed
@@ -34,9 +34,11 @@ contract CrossChainExecutorOptimism is ICrossChainExecutor {
    * @notice CrossChainExecutorOptimism constructor.
    * @param _crossDomainMessenger Address of the Optimism cross domain messenger on the Optimism chain
    */
-  constructor(ICrossDomainMessenger _crossDomainMessenger) {
+  constructor(ICrossDomainMessenger _crossDomainMessenger, ICrossChainRelayer _relayer) {
     require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");
+    require(address(_relayer) != address(0), "Executor/relayer-already-set");
     crossDomainMessenger = _crossDomainMessenger;
+    relayer = _relayer;
   }
 
   /* ============ External Functions ============ */
@@ -47,25 +49,14 @@ contract CrossChainExecutorOptimism is ICrossChainExecutor {
     address _sender,
     CallLib.Call[] calldata _calls
   ) external {
-    ICrossChainRelayer _relayer = relayer;
-    _isAuthorized(_relayer);
+    _isAuthorized(relayer);
 
     bool _executedNonce = executed[_nonce];
     executed[_nonce] = true;
 
     CallLib.executeCalls(_nonce, _sender, _calls, _executedNonce);
 
-    emit ExecutedCalls(_relayer, _nonce);
-  }
-
-  /**
-   * @notice Set relayer contract address.
-   * @dev Will revert if it has already been set.
-   * @param _relayer Address of the relayer contract on the Ethereum chain
-   */
-  function setRelayer(ICrossChainRelayer _relayer) external {
-    require(address(relayer) == address(0), "Executor/relayer-already-set");
-    relayer = _relayer;
+    emit ExecutedCalls(relayer, _nonce);
   }
 
   /* ============ Internal Functions ============ */
```

#### `src/ethereum-optimism/EthereumToOptimismRelayer.sol`

```diff
diff --git a/src/ethereum-optimism/EthereumToOptimismRelayer.sol b/src/ethereum-optimism/EthereumToOptimismRelayer.sol
index ac70501..d9ffd3d 100644
--- a/src/ethereum-optimism/EthereumToOptimismRelayer.sol
+++ b/src/ethereum-optimism/EthereumToOptimismRelayer.sol
@@ -20,7 +20,7 @@ contract CrossChainRelayerOptimism is ICrossChainRelayer {
   ICrossDomainMessenger public immutable crossDomainMessenger;
 
   /// @notice Address of the executor contract on the Optimism chain.
-  ICrossChainExecutor public executor;
+  ICrossChainExecutor public immutable executor;
 
   /// @notice Gas limit provided for free on Optimism.
   uint256 public immutable maxGasLimit;
@@ -35,11 +35,13 @@ contract CrossChainRelayerOptimism is ICrossChainRelayer {
    * @param _crossDomainMessenger Address of the Optimism cross domain messenger
    * @param _maxGasLimit Gas limit provided for free on Optimism
    */
-  constructor(ICrossDomainMessenger _crossDomainMessenger, uint256 _maxGasLimit) {
+  constructor(ICrossDomainMessenger _crossDomainMessenger, ICrossChainExecutor _executor, uint256 _maxGasLimit) {
     require(address(_crossDomainMessenger) != address(0), "Relayer/CDM-not-zero-address");
+    require(address(_executor) != address(0), "Relayer/executor-already-set");
     require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
 
     crossDomainMessenger = _crossDomainMessenger;
+    executor = _executor;
     maxGasLimit = _maxGasLimit;
   }
 
@@ -77,13 +79,4 @@ contract CrossChainRelayerOptimism is ICrossChainRelayer {
     return _nonce;
   }
 
-  /**
-   * @notice Set executor contract address.
-   * @dev Will revert if it has already been set.
-   * @param _executor Address of the executor contract on the Optimism chain
-   */
-  function setExecutor(ICrossChainExecutor _executor) external {
-    require(address(executor) == address(0), "Relayer/executor-already-set");
-    executor = _executor;
-  }
 }
```



## Use `calldata` instead of `memory` for read-only arguments functions saves gas (`CallLib.sol`)
For this you will need to create another function to support the call on polygon, but its worth it;

```
testExecuteCalls() (gas: -253 (-0.241%)) 
testRelayCalls() (gas: -14418 (-1.010%)) 
testExecuteCalls() (gas: -14671 (-1.051%)) 
testIsAuthorized() (gas: -14418 (-1.119%)) 
testGasLimitTooHigh() (gas: -14418 (-1.121%)) 
testSetGreetingError() (gas: -14418 (-1.124%)) 
testExecuteCallsAlreadyExecuted() (gas: -1245 (-1.253%)) 
testGreeter() (gas: -14418 (-1.659%)) 
testRelayer() (gas: -14424 (-1.683%)) 
testExecutor() (gas: -14424 (-1.684%)) 
Overall gas change: -117107 (-11.945%)
```

### Suggested changes;
#### `src/ethereum-polygon/EthereumToPolygonExecutor.sol`

```diff
diff --git a/src/ethereum-polygon/EthereumToPolygonExecutor.sol b/src/ethereum-polygon/EthereumToPolygonExecutor.sol
index 29bc54f..924bf7e 100644
--- a/src/ethereum-polygon/EthereumToPolygonExecutor.sol
+++ b/src/ethereum-polygon/EthereumToPolygonExecutor.sol
@@ -54,7 +54,7 @@ contract CrossChainExecutorPolygon is FxBaseChildTunnel {
     bool _executedNonce = executed[_nonce];
     executed[_nonce] = true;
 
-    CallLib.executeCalls(_nonce, _callsSender, _calls, _executedNonce);
+    CallLib.executeCallsMemory(_nonce, _callsSender, _calls, _executedNonce);
 
     emit ExecutedCalls(_sender, _nonce);
   }
```

#### `src/libraries/CallLib.sol`

```diff
diff --git a/src/libraries/CallLib.sol b/src/libraries/CallLib.sol
index 18d8cdc..52bad30 100644
--- a/src/libraries/CallLib.sol
+++ b/src/libraries/CallLib.sol
@@ -47,6 +47,31 @@ library CallLib {
    * @param _executedNonce Whether `_calls` have already been executed or not
    */
   function executeCalls(
+    uint256 _nonce,
+    address _sender,
+    Call[] calldata _calls,
+    bool _executedNonce
+  ) internal {
+    if (_executedNonce) {
+      revert CallsAlreadyExecuted(_nonce);
+    }
+
+    uint256 _callsLength = _calls.length;
+
+    for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
+      Call memory _call = _calls[_callIndex];
+
+      (bool _success, bytes memory _returnData) = _call.target.call(
+        abi.encodePacked(_call.data, _nonce, _sender)
+      );
+
+      if (!_success) {
+        revert CallFailure(_callIndex, _returnData);
+      }
+    }
+  }
+
+  function executeCallsMemory(
```
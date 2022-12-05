## Use bitmaps to save gas

During calls to `executeCalls` (and `processMessageFromRoot` in the Polygon executor) the `executed` mapping is updated, setting the bool flag to true, but because the way the EVM works it allocates an entire storage slot (256 bits) every time a new flag is set. `SSTORE` opcode cost up to 20000 gas for uninitialized slots like these. Consider using bitmaps instead, this enables you to convert the `mapping(uint256 => bool)` to a `mapping(uint256 => uint256)` and pay the cost of slot initialization just once every 256 `nonces` added, so the high gas costs are amortized over many calls.

Each group of 256 sequential `nonces` values (0-255, 256-511, ...) are stored together in a single uint256, where each bit represents a bool. The correct index of each nonce inside each bitmap can be calculated by `nonce mod 256`. An exemplified implementation is shown below (For complete implementations see Modifications section)

```javascript
mapping(uint256 => uint256) public executed;

//setting executed nonce value to true
uint256 baseIndex = _nonce / 256;
uint256 index = _nonce - (256 * baseIndex); 
uint256 mask = 1 << index;
executed[baseIndex] |= mask;

//getting the executed bool flag for a specific nonce
uint256 baseIndex = _nonce / 256;
uint256 index = _nonce - (256 * baseIndex); 
uint256 mask = 1 << index;
bool _executedNonce = (executed[baseIndex] & mask) != 0
```

The full extent of the gas reduction from these changes cannot be seen using the tests present in the repo, as during the tests `executeCalls` are called at most a few times, so the effect of amortization cannot be seen, underestimating the effect of these changes. Extra tests were added to enable benchmarking more realistic conditions (See modifications section below), where `executeCalls` is called 100 times in sequence, simulating many user interacting with the contract during normal operating conditions. Using those benchmarks, we obtain the following results.

|                                                    | avg. gas (before modification) | avg. gas (after modification) | gas diff         |
|----------------------------------------------------|--------------------------------|-------------------------------|------------------|
| CrossChainExecutorArbitrum - executeCalls          | 31322                          | 9980                          | -21342 (-68.1%)  |
| CrossChainExecutorOptimism - executeCalls          | 32241                          | 10899                         | -21342 (-66.2%)  |
| CrossChainExecutorPolygon - processMessageFromRoot | 32279                          | 10940                         | -21339 (-66.1%)  |
| Total                                              | 95842                          | 31819                         | -64023 (-66.8%)  |

*Values obtained by running `forge test --match-test Benchmark --gas-report`


### Modifications

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol
```diff
diff --git a/EthereumToArbitrumExecutor.sol.orig b/EthereumToArbitrumExecutor.sol
index bfee411..cbe299b 100644
--- a/EthereumToArbitrumExecutor.sol.orig
+++ b/EthereumToArbitrumExecutor.sol
@@ -23,7 +23,7 @@ contract CrossChainExecutorArbitrum is ICrossChainExecutor {
    *         nonce => boolean
    * @dev Ensure that batch of calls cannot be replayed once they have been executed.
    */
-  mapping(uint256 => bool) public executed;
+  mapping(uint256 => uint256) public executed;
 
   /* ============ External Functions ============ */
 
@@ -36,8 +36,9 @@ contract CrossChainExecutorArbitrum is ICrossChainExecutor {
     ICrossChainRelayer _relayer = relayer;
     _isAuthorized(_relayer);
 
-    bool _executedNonce = executed[_nonce];
-    executed[_nonce] = true;
+    (uint256 baseIndex, uint256 mask) = _getBaseIndexAndMask(_nonce);
+    bool _executedNonce = (executed[baseIndex] & mask) != 0;
+    executed[baseIndex] |= mask;
 
     CallLib.executeCalls(_nonce, _sender, _calls, _executedNonce);
 
@@ -54,6 +55,11 @@ contract CrossChainExecutorArbitrum is ICrossChainExecutor {
     relayer = _relayer;
   }
 
+  function isExecuted(uint256 _nonce) external view returns (bool) {
+    (uint256 baseIndex, uint256 mask) = _getBaseIndexAndMask(_nonce);
+    return (executed[baseIndex] & mask) != 0;
+  }
+
   /* ============ Internal Functions ============ */
 
   /**
@@ -67,4 +73,11 @@ contract CrossChainExecutorArbitrum is ICrossChainExecutor {
       "Executor/sender-unauthorized"
     );
   }
+
+  function _getBaseIndexAndMask(uint256 _nonce) internal view returns (uint256, uint256) {
+    uint256 baseIndex = _nonce / 256;
+    uint256 index = _nonce - (256 * baseIndex);
+    uint256 mask = 1 << index;
+    return (baseIndex, mask);
+  }
 }
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol
```diff
diff --git a/EthereumToOptimismExecutor.sol.orig b/EthereumToOptimismExecutor.sol
index 1aba9c1..6763b22 100644
--- a/EthereumToOptimismExecutor.sol.orig
+++ b/EthereumToOptimismExecutor.sol
@@ -26,7 +26,7 @@ contract CrossChainExecutorOptimism is ICrossChainExecutor {
    *         nonce => boolean
    * @dev Ensure that batch of calls cannot be replayed once they have been executed.
    */
-  mapping(uint256 => bool) public executed;
+  mapping(uint256 => uint256) public executed;
 
   /* ============ Constructor ============ */
 
@@ -50,8 +50,9 @@ contract CrossChainExecutorOptimism is ICrossChainExecutor {
     ICrossChainRelayer _relayer = relayer;
     _isAuthorized(_relayer);
 
-    bool _executedNonce = executed[_nonce];
-    executed[_nonce] = true;
+    (uint256 baseIndex, uint256 mask) = _getBaseIndexAndMask(_nonce);
+    bool _executedNonce = (executed[baseIndex] & mask) != 0;
+    executed[baseIndex] |= mask;
 
     CallLib.executeCalls(_nonce, _sender, _calls, _executedNonce);
 
@@ -68,6 +69,11 @@ contract CrossChainExecutorOptimism is ICrossChainExecutor {
     relayer = _relayer;
   }
 
+  function isExecuted(uint256 _nonce) external view returns (bool) {
+    (uint256 baseIndex, uint256 mask) = _getBaseIndexAndMask(_nonce);
+    return (executed[baseIndex] & mask) != 0;
+  }
+
   /* ============ Internal Functions ============ */
 
   /**
@@ -83,4 +89,11 @@ contract CrossChainExecutorOptimism is ICrossChainExecutor {
       "Executor/sender-unauthorized"
     );
   }
+
+  function _getBaseIndexAndMask(uint256 _nonce) internal view returns (uint256, uint256) {
+    uint256 baseIndex = _nonce / 256;
+    uint256 index = _nonce - (256 * baseIndex);
+    uint256 mask = 1 << index;
+    return (baseIndex, mask);
+  }
 }
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol
```diff
diff --git a/EthereumToPolygonExecutor.sol.orig b/EthereumToPolygonExecutor.sol
index 29bc54f..9c0df8c 100644
--- a/EthereumToPolygonExecutor.sol.orig
+++ b/EthereumToPolygonExecutor.sol
@@ -28,7 +28,7 @@ contract CrossChainExecutorPolygon is FxBaseChildTunnel {
    *         nonce => boolean
    * @dev Ensure that batch of calls cannot be replayed once they have been executed.
    */
-  mapping(uint256 => bool) public executed;
+  mapping(uint256 => uint256) public executed;
 
   /* ============ Constructor ============ */
 
@@ -51,11 +51,24 @@ contract CrossChainExecutorPolygon is FxBaseChildTunnel {
       (uint256, address, CallLib.Call[])
     );
 
-    bool _executedNonce = executed[_nonce];
-    executed[_nonce] = true;
+    (uint256 baseIndex, uint256 mask) = _getBaseIndexAndMask(_nonce);
+    bool _executedNonce = (executed[baseIndex] & mask) != 0;
+    executed[baseIndex] |= mask;
 
     CallLib.executeCalls(_nonce, _callsSender, _calls, _executedNonce);
 
     emit ExecutedCalls(_sender, _nonce);
   }
+
+  function isExecuted(uint256 _nonce) external view returns (bool) {
+    (uint256 baseIndex, uint256 mask) = _getBaseIndexAndMask(_nonce);
+    return (executed[baseIndex] & mask) != 0;
+  }
+
+  function _getBaseIndexAndMask(uint256 _nonce) internal view returns (uint256, uint256) {
+    uint256 baseIndex = _nonce / 256;
+    uint256 index = _nonce - (256 * baseIndex);
+    uint256 mask = 1 << index;
+    return (baseIndex, mask);
+  }
 }
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/test/unit/ethereum-arbitrum/EthereumToArbitrumExecutor.t.sol
```diff
diff --git a/EthereumToArbitrumExecutor.t.sol.orig b/EthereumToArbitrumExecutor.t.sol
index 942d92d..37e7161 100644
--- a/EthereumToArbitrumExecutor.t.sol.orig
+++ b/EthereumToArbitrumExecutor.t.sol
@@ -70,7 +70,7 @@ contract CrossChainExecutorArbitrumUnitTest is Test {
 
     executor.executeCalls(nonce, sender, calls);
 
-    assertTrue(executor.executed(nonce));
+    assertTrue(executor.isExecuted(nonce));
   }
 
   function testExecuteCallsAlreadyExecuted() public {
@@ -90,6 +90,17 @@ contract CrossChainExecutorArbitrumUnitTest is Test {
     executor.executeCalls(nonce, sender, calls);
   }
 
+  function testExecuteCallsBenchmark() public {
+    setRelayer();
+
+    vm.startPrank(relayerAlias);
+
+    for (uint256 i; i < 100; i++) {
+      executor.executeCalls(nonce, sender, calls);
+      nonce++;
+    }
+  }
+
   /* ============ Setters ============ */
 
   function testSetRelayer() public {
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/test/fork/EthereumToOptimismFork.t.sol
```diff
diff --git a/EthereumToOptimismFork.t.sol.orig b/EthereumToOptimismFork.t.sol
index be84235..ed2d2e7 100644
--- a/EthereumToOptimismFork.t.sol.orig
+++ b/EthereumToOptimismFork.t.sol
@@ -194,6 +194,39 @@ contract EthereumToOptimismForkTest is Test {
     assertEq(greeter.greet(), l1Greeting);
   }
 
+  function testExecuteCallsBenchmark() public {
+    deployAll();
+    setAll();
+
+    vm.selectFork(optimismFork);
+
+    CallLib.Call[] memory _calls = new CallLib.Call[](1);
+
+    _calls[0] = CallLib.Call({
+      target: address(greeter),
+      data: abi.encodeWithSignature("setGreeting(string)", l1Greeting)
+    });
+
+    L2CrossDomainMessenger l2Bridge = L2CrossDomainMessenger(l2CrossDomainMessenger);
+
+    vm.startPrank(AddressAliasHelper.applyL1ToL2Alias(proxyOVML1CrossDomainMessenger));
+
+    for (uint256 i; i < 100; i++) {
+      l2Bridge.relayMessage(
+        address(executor),
+        address(relayer),
+        abi.encodeWithSignature(
+          "executeCalls(uint256,address,(address,bytes)[])",
+          nonce,
+          address(this),
+          _calls
+        ),
+        l2Bridge.messageNonce() + 1
+      );
+      nonce++;
+    }
+  }
+
   function testGasLimitTooHigh() public {
     deployAll();
     setAll();
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/test/fork/EthereumToPolygonFork.t.sol
```diff
diff --git a/EthereumToPolygonFork.t.sol.orig b/EthereumToPolygonFork.t.sol
index 01913e6..eaad5f2 100644
--- a/EthereumToPolygonFork.t.sol.orig
+++ b/EthereumToPolygonFork.t.sol
@@ -179,6 +179,31 @@ contract EthereumToPolygonForkTest is Test {
     assertEq(greeter.greet(), l1Greeting);
   }
 
+  function testExecuteCallsBenchmark() public {
+    deployAll();
+    setAll();
+
+    vm.selectFork(polygonFork);
+
+    CallLib.Call[] memory _calls = new CallLib.Call[](1);
+
+    _calls[0] = CallLib.Call({
+      target: address(greeter),
+      data: abi.encodeWithSignature("setGreeting(string)", l1Greeting)
+    });
+
+    vm.startPrank(fxChild);
+
+    for (uint256 i; i < 100; i++) {
+      executor.processMessageFromRoot(
+        1,
+        address(relayer),
+        abi.encode(nonce, address(this), _calls)
+      );
+      nonce++;
+    }
+  }
+
   function testGasLimitTooHigh() public {
     deployAll();
```
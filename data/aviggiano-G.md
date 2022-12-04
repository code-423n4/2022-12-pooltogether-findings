# Avoid 1 SLOAD on relayer `nonce` increment

### Code diff

```solidity
diff --git a/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol b/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
index f88674d..60d425e 100644
--- a/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
+++ b/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol
@@ -75,9 +75,7 @@ contract CrossChainRelayerArbitrum is ICrossChainRelayer {
       revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
     }
 
-    nonce++;
-
-    uint256 _nonce = nonce;
+    uint256 _nonce = ++nonce;
 
     relayed[_getTxHash(_nonce, _calls, msg.sender, _gasLimit)] = true;
 
diff --git a/src/ethereum-polygon/EthereumToPolygonRelayer.sol b/src/ethereum-polygon/EthereumToPolygonRelayer.sol
index 2867314..dd18576 100644
--- a/src/ethereum-polygon/EthereumToPolygonRelayer.sol
+++ b/src/ethereum-polygon/EthereumToPolygonRelayer.sol
@@ -53,9 +53,7 @@ contract CrossChainRelayerPolygon is ICrossChainRelayer, FxBaseRootTunnel {
       revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
     }
 
-    nonce++;
-
-    uint256 _nonce = nonce;
+    uint256 _nonce = ++nonce;
 
     _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));
 

```

### Gas diff

```diff
testRelayCalls() (gas: -97 (-0.004%))
testProcessCalls() (gas: -100 (-0.079%))
testRelayCalls() (gas: -100 (-0.086%))
Overall gas change: -297 (-0.168%)
```
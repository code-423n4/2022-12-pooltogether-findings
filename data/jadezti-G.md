
## [GAS-1] Gas optimization for `relayCalls` function

Function `relayCalls`, in Contracts `CrossChainRelayerArbitrum, CrossChainRelayerOptimism, CrossChainRelayerPolygon`, can be optimized in four aspects for gas savings.
The four aspects are:
1. Using named return variable instead of unnamed return variable can save gas;
2. Caching `nonce` state variable as `_nonce` local variable earlier, and applying incrementation on the cached variable saves gas;
3.  `++_nonce` saves more gas than `_nonce`;
4. Applying `unchecked{}` to `++_nonce` saves more gas. Since `nonce` (cached as `_nonce`) is a `uint256` variable, which is incremented one by each successful `relayCalls` call starting from zero (and each call will cost gas --> flooding attack to overflow `nonce` is impractical), it is safe to say that `nonce`  won't overflow.

### Contract `CrossChainRelayerArbitrum` optimization

**Changes to function [`relayCalls`](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L67-L87) are as in the bellow:**
[Link to code](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L67-L87)
```js
  function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
    external
    payable
    returns (uint256 _nonce)  /// @audit - use named return variable
  {
    uint256 _maxGasLimit = maxGasLimit;

    if (_gasLimit > _maxGasLimit) {
      revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
    }

    // nonce++;

    // uint256 _nonce = nonce;

    /// @audit - add below lines to replace the above commented two lines
    _nonce = nonce;
    unchecked {
      nonce = ++_nonce;
    }

    relayed[_getTxHash(_nonce, _calls, msg.sender, _gasLimit)] = true;

    emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);

    /// @audit - remove the below return statement
    // return _nonce; 
  }
```

**Run `yarn test` to verify gas savings:**

Contract `CrossChainRelayerArbitrum` deployment cost:

| Deployment | Cost   | Size |
| ---------- | ------ | ---- |
| Before     | 487494 | 2846 |
| After      | 477081 | 2794 |
| Savings    | 10413  | 52   |

Function `relayCalls` running costs:

| relayCalls | min | avg   | median | max   |
| ---------- | --- | ----- | ------ | ----- |
| Before     | 561 | 34385 | 51298  | 51298 |
| After      | 561 | 34280 | 51140  | 51140 |
| Savings    | 0   | 105   | 158    | 158   |



### Contract CrossChainRelayerOptimism optimization

**Changes to function `relayCalls`:**
[Link to code](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L49-L78)
```js
  /// @inheritdoc ICrossChainRelayer
  function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
    external
    payable
    returns (
      uint256 _nonce /// @audit - use named return variable
    )
  {
    uint256 _maxGasLimit = maxGasLimit;

    if (_gasLimit > _maxGasLimit) {
      revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
    }

    // nonce++;

    // uint256 _nonce = nonce;

    /// @audit - replace the above two commented lines
    _nonce = nonce;
    unchecked {
      nonce = ++_nonce;
    }

    crossDomainMessenger.sendMessage(
      address(executor),
      abi.encodeWithSignature(
        "executeCalls(uint256,address,(address,bytes)[])",
        _nonce,
        msg.sender,
        _calls
      ),
      uint32(_gasLimit)
    );

    emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);

    /// @audit - remove the return statement
    // return _nonce;
  }

```

**Run `yarn test` to verify gas savings:**
Contract CrossChainRelayerOptimism deployment cost:

| Deployment | Cost   | Size |
| ---------- | ------ | ---- |
| Before     | 305313 | 1935 |
| After      | 295307 | 1885 |
| Savings    | 10006  | 50   |

Function `relayCalls` running costs:

| relayCalls | min | avg   | median | max    |
| ---------- | --- | ----- | ------ | ------ |
| Before     | 490 | 67542 | 67542  | 134594 |
| After      | 490 | 67462 | 67462  | 134435 |
| Savings    | 0   | 80    | 80     | 159     |


### Contract CrossChainRelayerPolygon optimization

**Changes to function `relayCalls`:**
[Link to code](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L45-L65)
```js
  /// @inheritdoc ICrossChainRelayer
  function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
    external
    payable
    returns (
      uint256 _nonce /// @audit - use named return variable
    )
  {
    uint256 _maxGasLimit = maxGasLimit;

    if (_gasLimit > _maxGasLimit) {
      revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
    }

    // nonce++; // @audit gas

    // uint256 _nonce = nonce;

    /// @audit - replace the above two commented lines
    _nonce = nonce;
    unchecked {
      nonce = ++_nonce;
    }

    _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));

    emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);

    /// @audit - remove return statement
    // return _nonce;
  }
```

**Run `yarn test` to verify gas savings:**
Contract CrossChainRelayerPolygon deployment cost savings

| Deployment | Cost    | Size |
| ---------- | ------- | ---- |
| Before     | 1668015 | 8526 |
| After      | 1665815 | 8515 |
| Savings    | 2200    | 11   |

Function `relayCalls` running costs savings:

| relayCalls | min | avg   | median | max   |
| ---------- | --- | ----- | ------ | ----- |
| Before     | 547 | 29545 | 29545  | 58544 |
| After      | 547 | 29464 | 29464  | 58382 |
| Savings    | 0   | 81    | 81     | 162   |


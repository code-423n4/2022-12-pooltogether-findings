**Executors**

The `executeCalls` functions should check if a `nounce` has already been executed before calling `CallLib.executeCalls(...)`. Yes, the responsibility of checking the above is passed to the `executeCalls` functions of each _executor_, both the `_executedNonce` parameter and the `if` statement of `CallLib.executeCalls(...)` can be removed, saving about __150 and 200 units of gas per call__ [see code](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L39) | [see code](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L55).

```solidity
// EthereumTo[...]Executor.sol
bool _executedNonce = executed[_nonce];
if (_executedNonce) revert CallLib.CallsAlreadyExecuted(_nonce);

executed[_nonce] = true;

// CallLib.executeCalls(_nonce, _sender, _calls, _executedNonce);
CallLib.executeCalls(_nonce, _sender, _calls);
```

```solidity
// CallLib.sol

function executeCalls(
    uint256 _nonce,
    address _sender,
    Call[] memory _calls // bool _executedNonce
  ) internal {
    // Can be removed...
    // if (_executedNonce) {
    //   revert CallsAlreadyExecuted(_nonce);
    // }

    uint256 _callsLength = _calls.length;

    for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
        Call memory _call = _calls[_callIndex];

        // (...)
    }
}
```

**Relayers**

If, in the `relayCalls` functions, instead of using the ++ as a suffix, it is used as a prefix and used in the assignment directly, __you can save around 100 gas units__. If, in addition, it is wrapped in an uncheck, __another 120 units of gas can be saved__ [see code](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78). 

```solidity
// EthereumTo[...]Relayer.sol

// nonce++;

uint256 _nonce = ++nonce;

// or

uint256 _nonce;
unchecked {
    _nonce = ++nonce;
}
```

**CallLib.sol**

In the loop inside the `executeCalls` function, the increment of the `_callIndex` index can be wrapped with an `unchecked`. With which you can save up to __50 gas units per iteration__. [see code](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61)

```solidity
function executeCalls(
    uint256 _nonce,
    address _sender,
    Call[] memory _calls,
    bool _executedNonce
  ) internal {
    // ...
    uint256 _callsLength = _calls.length;

    // for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
    for (uint256 _callIndex; _callIndex < _callsLength; ) {
      Call memory _call = _calls[_callIndex];

      (bool _success, bytes memory _returnData) = _call.target.call(
        abi.encodePacked(_call.data, _nonce, _sender)
      );

      if (!_success) {
        revert CallFailure(_callIndex, _returnData);
      }

      unchecked {
        ++_callIndex;
      }
    }
}
```
using `uncheck { ++_callIndex; }` can make the gas cheaper than `_callIndex++`

Code location:https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61
```
function executeCalls(
    uint256 _nonce,
    address _sender,
    Call[] memory _calls,
    bool _executedNonce
  ) internal {
    ...

    for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
    ...
    }
}
```

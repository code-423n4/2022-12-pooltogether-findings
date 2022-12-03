G1.  There is no need to introduce ``memory_call``, eliminating it can save gas.
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61
```
for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
           (bool _success, bytes memory _returnData) =  _calls[_callIndex].target.call(
        abi.encodePacked( _calls[_callIndex];.data, _nonce, _sender)
      );

      if (!_success) {
        revert CallFailure(_callIndex, _returnData);
      }
    }
  }
```
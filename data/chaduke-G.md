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

G2. https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L64
This function is called only once and can be inlined to save gas.

G3. https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L159
The body of ``_getTxHash`` can be used to replace this line to save gas. Unnecessary wrapping.


G4. https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L37
The line can be replaced by the body of ``_isAuthorized()`` to save gas. No need to implement it as a separate function, which is only one line of requirement statement. 




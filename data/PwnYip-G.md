
_executedNonce is placed in the CallLib.executeCalls function to perform the judgment logic, But when _executedNonce is true, it should revert before CallLib.executeCalls and there is no need to execute this `executed[_nonce] = true;`, This will consume even more Gas, So the judgment should be move to the following location(@audit). 

* src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol
```
  function executeCalls(
    uint256 _nonce,
    address _sender,
    CallLib.Call[] calldata _calls
  ) external {
    ICrossChainRelayer _relayer = relayer;
    _isAuthorized(_relayer);

    bool _executedNonce = executed[_nonce];
    // @audit add require(!_executedNonce); here
    executed[_nonce] = true;
    CallLib.executeCalls(_nonce, _sender, _calls, _executedNonce);
    emit ExecutedCalls(_relayer, _nonce);
  }
```

* src/ethereum-optimism/EthereumToOptimismExecutor.sol
```
  function executeCalls(
    uint256 _nonce,
    address _sender,
    CallLib.Call[] calldata _calls
  ) external {
    ICrossChainRelayer _relayer = relayer;
    _isAuthorized(_relayer);

    bool _executedNonce = executed[_nonce];
    // @audit add require(!_executedNonce); here
    executed[_nonce] = true;

    CallLib.executeCalls(_nonce, _sender, _calls, _executedNonce);

    emit ExecutedCalls(_relayer, _nonce);
  }
```

* src/ethereum-polygon/EthereumToPolygonExecutor.sol
```
  function _processMessageFromRoot(
    uint256, /* _stateId */
    address _sender,
    bytes memory _data
  ) internal override validateSender(_sender) {
    (uint256 _nonce, address _callsSender, CallLib.Call[] memory _calls) = abi.decode(
      _data,
      (uint256, address, CallLib.Call[])
    );

    bool _executedNonce = executed[_nonce];
    // @audit add require(!_executedNonce); here
    executed[_nonce] = true;
    
    CallLib.executeCalls(_nonce, _callsSender, _calls, _executedNonce);

    emit ExecutedCalls(_sender, _nonce);
  }
```

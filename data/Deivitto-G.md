# GAS
## splitting `require()` statements that use `&&` saves gas
### Summary
Instead of using the && operator in a single require statement to check multiple conditions, consider using multiple require statements with 1 condition per require statement (saving 3 gas per & ):
### Github Permalinks
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L80-L84

### Mitigation
Split require statements




## Increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks

https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/libraries/CallLib.sol#L61
    for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {


### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header


## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the abi.encode function to `abi.encodePacked` can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.
### Github Permalinks

https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L179
    return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L60
    _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));


### Mitigation
Consider changing abi.encode to `abi.encodePacked`


## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs `20` to `40` gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Github Permalinks
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L64
  function _isAuthorized(ICrossChainRelayer _relayer) internal view {
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L77
  function _isAuthorized(ICrossChainRelayer _relayer) internal view {




### Mitigation
Consider changing internal function only called once to inline code for gas savings
## assembly hash has can be used in order to save gas at hashing
### Impact
Over 80 gas savings at each call
Over 5000 gas savings at deployment

Deploy:
Case solidity: 36687
Case assembly: 31281

Gas at calls:
                AVG     MED         MAX         at 1 Call
Case solidity:  313     ┆ 313      ┆ 313 ┆ 
Case assembly:  231     ┆ 231      ┆ 231 ┆ 
### Details
Using the example of:
```
    function hashSolidity(uint256 a, uint256 b) public view {
        keccak256(abi.encodePacked(a, b));
    }
    function hashAssembly(uint256 a, uint256 b) public view {
        assembly {
            mstore(0x00, a)
            mstore(0x20, b)
            let hashedVal := keccak256(0x00, 0x40)
        }
    }
```
### Github Permalinks
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.solL179
### Mitigation
Consider using assembly for hashing

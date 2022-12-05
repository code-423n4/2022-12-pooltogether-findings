## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable. 

Here are the six instances entailed:

[File: EthereumToArbitrumRelayer.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol)

```
70:    returns (uint256)

108:  ) external payable returns (uint256) {

158:  ) external view returns (bytes32) {

178:  ) internal view returns (bytes32) {
```
[File: EthereumToOptimismRelayer.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol)

```
52:    returns (uint256)
```
[File: EthereumToPolygonRelayer.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol)

```
48:    returns (uint256)
```
original
[PASS] testProcessCalls() (gas: 126866)
new
[PASS] testProcessCalls() (gas: 126864)
## Split Require Statements Using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`. 

Here are the instances entailed:

[File: EthereumToOptimismExecutor.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol)

```
81:      msg.sender == address(_crossDomainMessenger) &&
```

no test for EthereumToOptimismExecutor.sol

## Unchecked SafeMath Saves Gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops.

When no arithmetic overflow/underflow is going to happen, `unchecked { _callIndex++ ;}` in the code block to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61-L71

```
    for (uint256 _callIndex; _callIndex < _callsLength;) {
      Call memory _call = _calls[_callIndex];

      (bool _success, bytes memory _returnData) = _call.target.call(
        abi.encodePacked(_call.data, _nonce, _sender)
      );

      if (!_success) {
        revert CallFailure(_callIndex, _returnData);
      }

      unchecked {
          _callIndex++;
      }
    }
```
no test for CallLib.sol

## `abi.encode()` is less efficient than `abi.encodePacked()`

```
    return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));
```
>File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol [(line 179)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L179)

```
    _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));
```
>File: src/ethereum-polygon/EthereumToPolygonRelayer.sol [(line 60)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L60)

## `internal` functions only called once can be `inlined` to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
  function _isAuthorized(ICrossChainRelayer _relayer) internal view {
```
>File: src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol [(line 64)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L64)

```
  function _isAuthorized(ICrossChainRelayer _relayer) internal view {
```
>File: src/ethereum-optimism/EthereumToOptimismExecutor.sol [(line 77)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L77)

## Splitting `require()` statements that use `&&` saves gas

Instead of using the `&&` operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per `&`):


```
    require(
      msg.sender == address(_crossDomainMessenger) &&
        _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
      "Executor/sender-unauthorized"
```
>File: src/ethereum-optimism/EthereumToOptimismExecutor.sol [(line 80-83)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L80-L83) 


## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.


```
      uint32(_gasLimit)
```
>File: src/ethereum-optimism/EthereumToOptimismRelayer.sol [(line 72)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L72)

## Empty  functions that are not used, can be removed to save deployment gas.

```
  function _processMessageFromChild(bytes memory data) internal override {
```
>File: src/ethereum-polygon/EthereumToPolygonRelayer.sol [(line 74)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L74)

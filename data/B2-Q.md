## Missing `zero address` check in `constructor`

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

```
  ) FxBaseRootTunnel(_checkpointManager, _fxRoot) {
```

>File: src/ethereum-polygon/EthereumToPolygonRelayer.sol [(line 37)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L37)

```
  constructor(address _fxChild) FxBaseChildTunnel(_fxChild) {}
```

>File: src/ethereum-polygon/EthereumToPolygonExecutor.sol [(line 39)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L39)

## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
	contract CrossChainRelayerPolygon is ICrossChainRelayer, FxBaseRootTunnel {
```
>File: src/ethereum-polygon/EthereumToPolygonRelayer.sol [(line 16)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L16)


##  Missing initializer modifier on constructor

OpenZeppelin recommends that the initializer modifier be applied to constructors in order to avoid potential griefs, social engineering, or exploits. Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.

```
	contract CrossChainRelayerPolygon is ICrossChainRelayer, FxBaseRootTunnel {
```
>File: src/ethereum-polygon/EthereumToPolygonRelayer.sol [(line 16)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L16)


## TYPOS

```
	///@audit: `idenfity`
   * @param nonce Nonce to uniquely idenfity the batch of calls
```
>File:src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol [(line 22)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L22)

```
	///@audit: `idenfity`
  /// @notice Nonce to uniquely idenfity each batch of calls.
```
>File:src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol [(line 39)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L39)

```
	Other instances of this issue are:
```
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L28
	///@audit: `idenfity`
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L22
	///@audit: `idenfity`
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L44
	///@audit: `idenfity`

## `NatSpec` is incomplete

```
  /// @audit Missing:  `@return` & `@param`
  /// @inheritdoc ICrossChainRelayer
  function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
```
> File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol [(line 66-67)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L66-L67)
```
  /// @audit Missing:  `@return` & `@param`
  /// @inheritdoc ICrossChainExecutor
  function executeCalls(
```
> src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol [(line 30-31)](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L30-L31)

```
	Other instances of this issue are:
```
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L48
  /// @audit Missing:  `@return` & `@param`
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L44
  /// @audit Missing:  `@return` & `@param`
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L44
  /// @audit Missing:  `@return` & `@param`
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L74
  /// @audit Missing: `@param`
* https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L43
  /// @audit Missing: `@param`
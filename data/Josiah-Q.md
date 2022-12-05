## CODEHASH CHECKS
It is recommended adding an optional and complementary codehash check for immutable addresses at the constructor since the zero address check cannot guarantee a matching/correct input address.

Here are the 3 instances found.

[EthereumToArbitrumRelayer.sol#L57](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L57)

```
    require(address(_inbox) != address(0), "Relayer/inbox-not-zero-address");
```
[EthereumToOptimismRelayer.sol#L39](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L39)

```
    require(address(_crossDomainMessenger) != address(0), "Relayer/CDM-not-zero-address");
```
[EthereumToOptimismExecutor.sol#L38](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L38)

```
    require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");
```
## CONTRACT EXISTENCE CHECK
Low-level calls do not check for contract existence. They are known to return success even though no function call has been executed when involving contract that may not have been deployed or have been self-destructed.

Here is the 1 instance found.

[CallLib.sol#L64](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L64)

```
      (bool _success, bytes memory _returnData) = _call.target.call(
```
## ZERO ADDRESS CHECKS
Zero address checks implemented at the constructor could avoid human errors leading to non-functional calls associated with the mistakes. This is especially so when the incidents entail immutable variables preventing them from getting reassigned that could end up having the protocol redeploy the contract.

Here are the 3 instances found.

[EthereumToPolygonRelayer.sol#L34-L35](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L34-L35)

```
    address _checkpointManager,
    address _fxRoot,
```
[EthereumToPolygonExecutor.sol#L39](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L39)

```
  constructor(address _fxChild) FxBaseChildTunnel(_fxChild) {}
```
## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more.

Here are the instances found.

[EthereumToArbitrumRelayer.sol#L67](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L67)

```
  function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
```
[EthereumToArbitrumExecutor.sol#L31](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L31)

```
  function executeCalls(
```
[EthereumToOptimismRelayer.sol#L49](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L49)

```
  function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
```
[EthereumToOptimismExecutor.sol#L45](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L45)

```
  function executeCalls(
```
[EthereumToPolygonRelayer.sol#L45](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L45)

```
  function relayCalls(CallLib.Call[] calldata _calls, uint256 _gasLimit)
```
[EthereumToPolygonExecutor.sol#L44](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L44)

```
  function _processMessageFromRoot(
```
## COMMENT AND CODE MISMATCH
The NatSpec of `setRelayer()` and `setExecuter()` says as follows:

```
   * @dev Will revert if it has already been set.
```
But according to the function logic, it reverts only when the zero address check fails. 

It is recommended changing the comments or fixing the code block to its intended purpose.

Here are the 4 instances found.

[EthereumToArbitrumRelayer.sol#L139](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L139)

```
  function setExecutor(ICrossChainExecutor _executor) external {
```
[EthereumToArbitrumExecutor.sol#L52](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52)

```
  function setRelayer(ICrossChainRelayer _relayer) external {
```
[EthereumToOptimismRelayer.sol#L85](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L85)

```
  function setExecutor(ICrossChainExecutor _executor) external {
```
[EthereumToOptimismExecutor.sol#L66](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L66)

```
  function setRelayer(ICrossChainRelayer _relayer) external {
```
## DOS ON UNBOUNDED LOOP
Unbounded loop could lead to OOG (Out of Gas) denying the users of needed services. It is recommended setting a threshold for the array length if the array could grow to or entail an excessive size.

Here is the 1 instance found.

[CallLib.sol#L61](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61)

```
    for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```
## BOUNDARY CHECKS
It is recommended setting boundary limits on zero value checks that concern immutable variables at the constructor.

This will avoid having extreme value as little as 1 or an infinitely large number assigned to the variables.

Here are the 3 instances found.

[EthereumToArbitrumRelayer.sol#L58](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L58)

```
    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```
[EthereumToOptimismRelayer.sol#L40](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L40)

```
    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```
[EthereumToPolygonRelayer.sol#L38](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L38)

```
    require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```
## MISSING EVENTS ON CRITICAL OPERATIONS
Several critical operations do not trigger events, which will make it difficult to review the correct behavior of the contracts once deployed. Users and blockchain monitoring systems will not be able to easily detect suspicious behaviors without events. 

Here are the 4 instances found.

[EthereumToArbitrumRelayer.sol#L139](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L139)

```
  function setExecutor(ICrossChainExecutor _executor) external {
```
[EthereumToArbitrumExecutor.sol#L52](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52)

```
  function setRelayer(ICrossChainRelayer _relayer) external {
```
[EthereumToOptimismRelayer.sol#L85](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L85)

```
  function setExecutor(ICrossChainExecutor _executor) external {
```
[EthereumToOptimismExecutor.sol#L66](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L66)

```
  function setRelayer(ICrossChainRelayer _relayer) external {
```
## MISSING SMART CONTRACT CHECKS
The NatSpec of `setRelayer()` and `setExecuter()` says as follows:

```
   * @param _executor Address of the executor contract on the Arbitrum chain
   * @param _relayer Address of the relayer contract on the Ethereum chain
```
It is recommended implementing a `extcodesize()` logic in the code block just to make sure the function argument inputs are indeed contract addresses instead of EOA addresses.
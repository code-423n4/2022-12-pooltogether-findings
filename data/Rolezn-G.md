## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 1 | 35 |
| [GAS&#x2011;2](#GAS&#x2011;2) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 4 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | `abi.encode()` is less efficient than `abi.encodepacked()` | 2 | 200 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Use calldata instead of memory for function parameters | 1 | 300 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Optimize names to save gas | 6 | 132 |
| [GAS&#x2011;6](#GAS&#x2011;6) | `internal` functions only called once can be inlined to save gas | 3 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | Setting the `constructor` to `payable` | 5 | 65 |
| [GAS&#x2011;8](#GAS&#x2011;8) | `relayer` can be set as immutable and set in `constructor` to save gas | 2 | 4000 |

Total: 24 contexts over 8 issues

## Gas Optimizations


### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
61: for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61





### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```solidity
53: require(address(relayer) == address(0), "Executor/relayer-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L53

```solidity
140: require(address(executor) == address(0), "Relayer/executor-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L140

```solidity
67: require(address(relayer) == address(0), "Executor/relayer-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L67

```solidity
86: require(address(executor) == address(0), "Relayer/executor-already-set");
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L86





### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
179: return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L179

```solidity
60: _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L60





### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:
```
contract C {
	function add(uint[] memory arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:
```
contract C {
	function add(uint[] calldata arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:
```
function f(bytes memory data) external {
	(...) = abi.decode(data, (..., types, ...));
}
```
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>


```solidity
function executeCalls(
    uint256 _nonce,
    address _sender,
    Call[] memory _calls,
    bool _executedNonce
  ) internal {
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L49






### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

```solidity
File: \ERC5164\src\ethereum-arbitrum\EthereumToArbitrumExecutor.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol

```solidity
File: \ERC5164\src\ethereum-arbitrum\EthereumToArbitrumRelayer.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

```solidity
File: \ERC5164\src\ethereum-optimism\EthereumToOptimismExecutor.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol

```solidity
File: \ERC5164\src\ethereum-optimism\EthereumToOptimismRelayer.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol

```solidity
File: \ERC5164\src\ethereum-polygon\EthereumToPolygonExecutor.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol

```solidity
File: \ERC5164\src\ethereum-polygon\EthereumToPolygonRelayer.sol
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol



#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.




### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
64: function _isAuthorized

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L64

```solidity
77: function _isAuthorized

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L77

```solidity
44: function _processMessageFromRoot

```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L44







### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Setting the `constructor` to `payable`

Saves ~15 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
56: constructor(IInbox _inbox, uint256 _maxGasLimit)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L56

```solidity
37: constructor(ICrossDomainMessenger _crossDomainMessenger)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L37

```solidity
38: constructor(ICrossDomainMessenger _crossDomainMessenger, uint256 _maxGasLimit)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L38

```solidity
39: constructor(address _fxChild) FxBaseChildTunnel(_fxChild)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L39

```solidity
33: constructor(
    address _checkpointManager,
    address _fxRoot,
    uint256 _maxGasLimit
  ) FxBaseRootTunnel(_checkpointManager, _fxRoot)
```

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L33


### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> `relayer` can be set as immutable and set in `constructor` to save gas

Current implementation of the relayer sets its value via the `setRelayer` function.
The value of the `relayer` can be set in the constructor and also set it as an `immutable` since the value of `relayer` cannot change once it has been set due to the `require(address(relayer) == address(0), "Executor/relayer-already-set");` statement.

#### <ins>Proof Of Concept</ins>

https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol
https://github.com/pooltogether/ERC5164/tree/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol

#### <ins>Recommended Mitigation Steps</ins>

Set `relayer` as immutable:

```solidity
ICrossChainRelayer public immutable relayer;
```

And set the relayer in the constructor:

```solidity
constructor(ICrossDomainMessenger _crossDomainMessenger, ICrossChainRelayer _relayer) {
    require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");
    crossDomainMessenger = _crossDomainMessenger;
    require(address(relayer) == address(0), "Executor/relayer-already-set");
    relayer = _relayer;
  }
```
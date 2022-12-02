

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol::19 => ICrossChainRelayer public relayer;
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::34 => ICrossChainExecutor public executor;
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::22 => ICrossChainRelayer public relayer;
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::23 => ICrossChainExecutor public executor;
```






### [G02] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::58 => require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::40 => require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::38 => require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```




### [G03] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)


#### Findings:
```
ERC5164/src/libraries/CallLib.sol::61 => for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```


### [G04] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
### verhead

> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::72 => uint32(_gasLimit)
```



### [G05] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol::53 => require(address(relayer) == address(0), "Executor/relayer-already-set");

ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::57 => revert GasLimitTooHigh(_gasLimit, _maxGasLimit);
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::38 => require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::53 => revert GasLimitTooHigh(_gasLimit, _maxGasLimit);

```


###  [G06] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol::53 => require(address(relayer) == address(0), "Executor/relayer-already-set");
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::57 => require(address(_inbox) != address(0), "Relayer/inbox-not-zero-address");
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::58 => require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::109 => require(relayed[_getTxHash(_nonce, _calls, _sender, _gasLimit)], "Relayer/calls-not-relayed");
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::140 => require(address(executor) == address(0), "Relayer/executor-already-set");
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::38 => require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::67 => require(address(relayer) == address(0), "Executor/relayer-already-set");
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::39 => require(address(_crossDomainMessenger) != address(0), "Relayer/CDM-not-zero-address");
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::40 => require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::86 => require(address(executor) == address(0), "Relayer/executor-already-set");
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::38 => require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
```


### [G07] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.
#### Findings:
```
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::74 => function _processMessageFromChild(bytes memory data) internal override {
```






### [G08] Using `bools` for storage incurs overhead


#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol::26 => mapping(uint256 => bool) public executed;
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::47 => mapping(bytes32 => bool) public relayed;
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::29 => mapping(uint256 => bool) public executed;
ERC5164/src/ethereum-polygon/EthereumToPolygonExecutor.sol::31 => mapping(uint256 => bool) public executed;
```




### [G09] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
ERC5164/src/ethereum-polygon/EthereumToPolygonExecutor.sol::39 => constructor(address _fxChild) FxBaseChildTunnel(_fxChild) {}
```


### [G10] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::179 => return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::60 => _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));
```



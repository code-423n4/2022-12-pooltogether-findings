### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Using storage instead of memory for arrays saves gas | 1 |
| [G-02] | Use ``assembly`` to write _address storage values_  | 3 |
| [G-03] | Setting The Constructor To Payable | 5 |
| [G-04] | Optimize names to save gas| All contracts |
| [G-05] | Sort Solidity operations using short-circuit mode | 1|
| [G-06] | Use ``double require`` instead of using ``&&``  | 1 |
| [G-07] | Empty blocks should be removed or emit something   | 2 |
| [G-08] | internal functions only called once can be inlined to save gas| 5 |

Total 8 issues
### [G-01] Using storage instead of memory for arrays saves gas

**Context:**

```solidity
File: src/ethereum-polygon/EthereumToPolygonExecutor.sol:

  49:     (uint256 _nonce, address _callsSender, CallLib.Call[] memory _calls) = abi.decode(
```

**Description:**
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. 


**Recommendation:**

Current Code:
```js
EthereumToPolygonExecutor.sol#L49

uint256 _nonce, address _callsSender, CallLib.Call[] memory _calls) = abi.decode(

```
Recommendation Code:
```js
EthereumToPolygonExecutor.sol#L49

uint256 _nonce, address _callsSender, CallLib.Call[] storage _calls) = abi.decode(
```
### [G-02] Use ``assembly`` to write _address storage values_ 


**Context:**

```solidity
3 result - 3 files

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:

  56:   constructor(IInbox _inbox, uint256 _maxGasLimit) {
  60:     inbox = _inbox;

src/ethereum-optimism/EthereumToOptimismExecutor.sol:

  37:   constructor(ICrossDomainMessenger _crossDomainMessenger) {
  39:     crossDomainMessenger = _crossDomainMessenger;

src/ethereum-optimism/EthereumToOptimismRelayer.sol:

  38:   constructor(ICrossDomainMessenger _crossDomainMessenger, uint256 _maxGasLimit) {
  42:     crossDomainMessenger = _crossDomainMessenger;
```

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {

    Contract1 c1;
    Contract2 c2;

    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }

    function testGas() public {
        c1.setArbitrum1(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4);
        c2.setArbitrum2(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4);
    }
}


contract Contract1  {
    address inbox;

    function setArbitrum1(address _inbox) external {
        inbox = _inbox;
    }
}


contract Contract2  {
    address inbox;

    function setArbitrum2(address _inbox) external {
        assembly {
            sstore(inbox.slot, _inbox)    
        }
    }
}
```
**Gas Report:**
```js
| src/test/test.sol:Contract1 contract |                 |       |        |       |         |
|--------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                      | Deployment Size |       |        |       |         |
| 48499                                | 273             |       |        |       |         |
| Function Name                        | min             | avg   | median | max   | # calls |
| setArbitrum1                         | 22363           | 22363 | 22363  | 22363 | 1       |


| src/test/test.sol:Contract2 contract |                 |       |        |       |         |
|--------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                      | Deployment Size |       |        |       |         |
| 35287                                | 207             |       |        |       |         |
| Function Name                        | min             | avg   | median | max   | # calls |
| setArbitrum2                         | 22324           | 22324 | 22324  | 22324 | 1       |
```

### [G-03] Setting The Constructor To Payable

**Description:**
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
```solidity
5 results - 5 files

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:
  55     */
  56:   constructor(IInbox _inbox, uint256 _maxGasLimit) {
  57      require(address(_inbox) != address(0), "Relayer/inbox-not-zero-address");

src/ethereum-optimism/EthereumToOptimismExecutor.sol:
  36     */
  37:   constructor(ICrossDomainMessenger _crossDomainMessenger) {
  38      require(address(_crossDomainMessenger) != address(0), "Executor/CDM-not-zero-address");

src/ethereum-optimism/EthereumToOptimismRelayer.sol:
  37     */
  38:   constructor(ICrossDomainMessenger _crossDomainMessenger, uint256 _maxGasLimit) {
  39      require(address(_crossDomainMessenger) != address(0), "Relayer/CDM-not-zero-address");

src/ethereum-polygon/EthereumToPolygonExecutor.sol:
  38     */
  39:   constructor(address _fxChild) FxBaseChildTunnel(_fxChild) {}
  40  

src/ethereum-polygon/EthereumToPolygonRelayer.sol:
  32     */
  33:   constructor(
  34      address _checkpointManager,
```

**Recommendation:**
Set the constructor to ```payable```

**Proof Of Concept:**
https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5?u=pcaversaccio

The optimizer was turned on and set to 10000 runs

```js
contract GasTestFoundry is DSTest {
    
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.x();
        c2.x();
    }
}

contract Contract1 {
    
    uint256 public dummy;
    constructor() payable {
        dummy = 1;
    }
    
    function x() public {

    }
}

contract Contract2 {
    
    uint256 public dummy;
    constructor() {
        dummy = 1;
    }
    
    function x() public {
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49563                                     ┆ 159             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤

╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49587                                     ┆ 172             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
```
### [G-04] Optimize names to save gas
**Context:** 
All Contracts

**Description:** 
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` EthereumToArbitrumRelayer.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

EthereumToArbitrumRelayer.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
9817d4b2  =>  relayCalls(CallLib.Call[],uint256)
8303190b  =>  processCalls(uint256,CallLib.Call[],address,uint256,uint256,uint256)
fe5681bd  =>  setExecutor(ICrossChainExecutor)
d4f3d8cc  =>  getTxHash(uint256,CallLib.Call[],address,uint256)
da1cef77  =>  _getTxHash(uint256,CallLib.Call[],address,uint256)
```
### [G-05] Sort Solidity operations using short-circuit mode

**Description:**
Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

**Context:**
```solidity
1 result - 1 file

src/ethereum-optimism/EthereumToOptimismExecutor.sol:
  80      require(
  81:       msg.sender == address(_crossDomainMessenger) &&
  82          _crossDomainMessenger.xDomainMessageSender() == address(_relayer),

```

### [G-06] Use ``double require`` instead of using ``&&``

**Context:** 
```solidity
1 result - 1 file

src/ethereum-optimism/EthereumToOptimismExecutor.sol:
  80      require(
  81:       msg.sender == address(_crossDomainMessenger) &&
  82          _crossDomainMessenger.xDomainMessageSender() == address(_relayer),

```

**Description:**
Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

**Recommendation:**
EthereumToOptimismExecutor.sol:L#80;
 ```js
    require(
      msg.sender == address(_crossDomainMessenger) &&
       _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
```

Recommendation Code:
```js
    require(msg.sender == address(_crossDomainMessenger), “Invalid crossDomainMessenger”);
    require(_crossDomainMessenger.xDomainMessageSender() == address(_relayer),” Invalid relayer”)
```

### [G-07] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.


**Context:** 
```solidity
2 results - 2 files

src/ethereum-polygon/EthereumToPolygonExecutor.sol:
  38     */
  39:   constructor(address _fxChild) FxBaseChildTunnel(_fxChild) {}
  40  

src/ethereum-polygon/EthereumToPolygonRelayer.sol:
  73     */
  74:   function _processMessageFromChild(bytes memory data) internal override {
  75:     /// no-op
  76:   }
```
### [G-08] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

**Context:** 
```solidity
5 results - 5 files

src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:

  64:   function _isAuthorized(ICrossChainRelayer _relayer) internal view {
  65      require(

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:

  177      uint256 _gasLimit
  178:   ) internal view returns (bytes32) {
  179      return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));

src/ethereum-optimism/EthereumToOptimismExecutor.sol:

  77:   function _isAuthorized(ICrossChainRelayer _relayer) internal view {

src/ethereum-polygon/EthereumToPolygonExecutor.sol:
 
  44  function _processMessageFromRoot(
  45  uint256, /* _stateId */
  46  address _sender,
  47      bytes memory _data
  48:   ) internal override validateSender(_sender) {
  49      (uint256 _nonce, address _callsSender, CallLib.Call[] memory _calls) = abi.decode(

src/ethereum-polygon/EthereumToPolygonRelayer.sol:

  74:   function _processMessageFromChild(bytes memory data) internal override {```

# Gas Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[G-01]|`uint256` Iterator Checked Each Iteration|1|
|[G-02]|&& In Require Statement(s)|1|

### [G-01] `uint256` Iterator Checked Each Iteration

A `uint256` iterator will not overflow. Unchecking the iterator increment saves gas.
**Example**
```solidity
//From
for (uint256 i; i < len; ++i) {
	//Do Something
}
//To
for (uint256 i; i < len;) {
	//Do Something
	unchecked { ++i; }
}
```

#### Findings:

*/src/libraries/CallLib.sol*
Links: [61](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61).
```solidity
61:	for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```

### [G-02] && In Require Statement(s)

Seperating require statements saves gas.
**Example**
```solidity
//From
require(a != HIGH && b != LOW);
//To
require(a != HIGH);
require(b != LOW);
```

#### Findings:

*/src/ethereum-optimism/EthereumToOptimismExecutor.sol*
Links: [80-84](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L80-L84).
```solidity
80:	require(
81:		msg.sender == address(_crossDomainMessenger) &&
82:		 _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
83:		"Executor/sender-unauthorized"
84:	);
```
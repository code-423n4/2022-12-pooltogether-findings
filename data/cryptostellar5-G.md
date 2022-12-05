### G-01 SPLITTING REQURE() STATEMENTS THAT USE && SAVES GAS

*Number of Instances Identified: 1*

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol

```
80 require(
      msg.sender == address(_crossDomainMessenger) &&
        _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
      "Executor/sender-unauthorized"
```


### G-02 INCREMENTS CAN BE UNCHECKED

*Number of Instances Identified: 1*

In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behavior, an unchecked block can be used


https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol

```
61: for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```

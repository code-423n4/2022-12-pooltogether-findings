## 2. Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ has an implicit overflow and underflow check on unsigned integers. When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

There is 1 instance of this issue:

[Line 61-71](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61-L71)

## 1. Splitting `require()` Statements That Use `&&` Saves Gas - (Saves ~`3` Gas per `&&`)

Instead of using the `&&` operator in a single require statement to check multiple conditions, using various require statements with 1 condition per require statement will save ~3 GAS per `&&`.

There is 1 instance of this issue:

[Line 80-84](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L80-L84)

```
    require(
      msg.sender == address(_crossDomainMessenger) &&
        _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
      "Executor/sender-unauthorized"
    );
```



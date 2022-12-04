## Nonce is read twice

Across three contracts (`EthereumToArbitrumRelayer.sol`, `EthereumToOptimismRelayer`, `EthereumToPolygonRelayer`), when the nonce is changed, it is first incremented and then assigned to a local variable `_nonce` in order to make a sha256 hash of it to ensure of the uniqueness of the call.
This results in two SLOADs after that the contract has been compiled, and those are especially expensive.
A fix would be to do:

```solidity
uint256 _nonce = nonce++;
```

This results in only one `SLOAD`, but the nonce will be incremented after assigning to `_nonce`. This must be taken into consideration because it slightly change the contract behaviour. In order to leave it the same, initializing the `nonce` to 1 seems to be a solution. As a first sight, this seems to be even less expensive:

```solidity
uint256 _nonce = ++nonce;
```

But it does two SLOADs as well. It is a shorthand of the original version, does not change the logic, though.
Unchecked version also SLOAD two times. 

These are the results of this gas optimisation round:

```shell
$ forge test

[⠒] Compiling...
[⠘] Compiling 1 files with 0.8.17
[⠃] Solc 0.8.17 finished in 534.88ms
Compiler run successful

Running 6 tests for test/Counter.t.sol:CounterTest
[PASS] test1() (gas: 27700)
[PASS] test2() (gas: 10481)
[PASS] test3() (gas: 27538)
[PASS] test4() (gas: 10334)
[PASS] test5() (gas: 27434)
[PASS] testEq() (gas: 103008)
Test result: ok. 6 passed; 0 failed; finished in 3.51ms
```

The best contender seems to be the `uint256 _state = state++` wrapped in an `unchecked` block, because this value will never overflow.

Please find the gist attached:

https://gist.github.com/iFrostizz/820ae069cf2890d5061ec86cbfad6d71




# Gas Optimisations

## 1. Clean up relayed mapping after calls are processed

The `processCalls()` function in the `EthereumToArbitrumRelayer` contract does not reset the value in the `relayed` mapping to `false` after processing the calls.
Doing so provides a [gas refund](https://soliditydeveloper.com/design-pattern-solidity-free-up-unused-storage), and so implementing this results in a gas saving.
Simply alter the `processCalls()` function slightly as follows:

```
function processCalls(...) external payable returns (uint256) {
    bytes32 _txHash = _getTxHash(_nonce, _calls, _sender, _gasLimit);
    require(relayed[_txHash], "Relayer/calls-not-relayed");

    delete relayed[_txHash];

    // ...

    return _ticketID;
}
```

This results in a gas saving of 1319 (16%):

| Function Name       | min  | avg  | median | max  | # calls |
| ------------------- | ---- | ---- | ------ | ---- | ------- |
| processCalls        | 8066 | 8066 | 8066   | 8066 | 1       |
| processCallsCheaper | 6744 | 6744 | 6744   | 6744 | 1       |

Instances: 1

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L101

## 2. Use unchecked for nonce incrementation

Use `unchecked{ ++nonce }` instead of `nonce++`. The automated gas report mentioned that `++nonce` is cheaper than `nonce++`, however did not mention to use `unchecked{}`.
This is safe because the operation cannot overflow.

Gas saving per call of `relayCalls()`:

- `EthereumToArbitrumRelayer.sol` - 130 gas
- `EthereumToOptimismRelayer.sol` - 124 gas
- `EthereumToPolygonRelayer.sol` - 146 gas

Instances: 3

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56

## 3. Cache `msg.sender` variable

```
function relayCalls(...) external payable returns (uint256) {

    // ...

    address _msgSender = msg.sender;

    _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));

    emit RelayedCalls(_nonce, msg.sender, _calls, _gasLimit);

    return _nonce;
}
```

Gas saving per call of `relayCalls()`:

- `EthereumToArbitrumRelayer.sol` - 14 gas
- `EthereumToOptimismRelayer.sol` - 11 gas
- `EthereumToPolygonRelayer.sol` - 36 gas

Instances: 3

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L82-L84
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L64-L75
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L60-L62

## 4. Use getter function for mappings instead of public keyword

Gas saving per call to view `executed[nonce]`:

- `EthereumToArbitrumExecutor.sol` - 77 gas
- `EthereumToOptimismExecutor.sol` - 77 gas
- `EthereumToPolygonExecutor.sol` - 99 gas

Instances: 4

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L26
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L29
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L31
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L47

## 5. Use `unchecked { _callIndex++ }` in for loop body in `CallLib.executeCalls()`

For loop index cannot overflow and so it is safe to use unchecked to save gas.
Also, replace `_callIndex < _callsLength` with `_callIndex != _callsLength`.
Using `!=` instead of `<` was mentioned in the automated report, however this instance was missed.

```
function executeCalls(...) internal {

    //...

    for (uint256 _callIndex; _callIndex != _callsLength; ) {

        //...

        unchecked{
            ++_callIndex;
        }
    }
}
```

Gas saved: - 69 gas per iteration

Deployment saving - 47 gas

Instances: 1

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L61

## 6. Remove/Replace `require()` statements in constructor

The team has mentioned the willingness to re-deploy the contracts if `setExecutor()` or `setRelayer()` are front-run.
Similarly, they could do the same if they mistakenly pass the zero address/zero uint to the constructor.
Removing the `require()` statement will save gas on deployment and sacrifice very little (misclicking constructor arguments is very unlikely).
Alternatively, the checks could be performed off chain prior to deployment.
However if they are to be kept, replacing them with custom errors would also save gas.

Deployment gas savings:

- `EthereumToArbitrumRelayer.sol` - 171
- `EthereumToOptimismExecutor.sol` - 98
- `EthereumToOptimismRelayer.sol` - 171
- `EthereumToPolygonRelayer.sol` - 86

Instances: 4

https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L57-L58
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L39-L40
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L38
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L38

## 7. Initialize `nonce` to `1` instead of `0` to save gas on the first call

Incrementing from zero to non-zero is more expensive than from non-zero to non-zero.

Gas saving on first call to `relayCalls()`:

- `EthereumToArbitrumExecutor.sol` - 17100 gas
- `EthereumToOptimismExecutor.sol` - 21900 gas
- `EthereumToPolygonExecutor.sol` - 21900 gas

Instances: 3

https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L40
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L29
https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L23

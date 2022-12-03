Per the sponsor's requirements, each optimization's actual gas report will be included. All gas findings are tested independently of each other, without taking other findings into account.

Initial gas report:
- https://gist.github.com/midori-fuse/fc0bf980ba7a426240b7e87cbc62977e

Final gas report (after applying all listed findings):
- https://gist.github.com/midori-fuse/76fff06349701ae69066044dfb9416b0

### Gas optimizations list

| Number |Details|Instances|
|:--:|:-------|:--:|
|[G-01]| `nonce` can be `uint96` to optimize storage read/writes | 2 |
|[G-02]| Increments can be unchecked (and made postfix) | 4 |

## [G-01] `nonce` can be `uint96` to optimize storage read/writes

Two applicable instances:
```solidity
File: src/ethereum-optimism/EthereumToOptimismRelayer.sol

29:   uint256 internal nonce;
```
```solidity
File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

40:   uint256 internal nonce;
```

The current storage layout for `CrossChainRelayerArbitrum` and `CrossChainRelayerOptimism` are as follow:

```solidity=
ICrossChainExecutor public executor; // storage slot 1
uint256 internal nonce; // storage slot 2
```

Interfaces are stored as an address in the storage, taking 160 bits. Therefore we can change `nonce` data type to `uint96` to fit in one storage slot. 

```solidity=
ICrossChainExecutor public executor; // storage slot 1
uint96 internal nonce; // still storage slot 1
```

The change brings the following optimizations to `relayCalls()`:
- Saves ~$20000$ gas for the first message by avoiding a Gsset, for the slot is already non-zero with an executor.
- For `CrossChainRelayerOptimism`, saves a further ~$2000$ gas by replacing a Gcoldsload with Gwarmaccess, for both executor and nonce are accessed.

This also should not bring any security issues, given the maximum value for `uint96` is approx. $10^{27}$, which is more than enough.

Gas report for G-01: https://gist.github.com/midori-fuse/df2a7b756502d159b254c64a212c39b3

## [G-02] Increments can be unchecked (and made postfix)

This finding builds upon **[GAS-4]** of C4 automated tool.

Four applicable instances (same with [GAS-4]):
```solidity
File: src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

78:     nonce++;
```

```solidity
File: src/ethereum-optimism/EthereumToOptimismRelayer.sol

60:     nonce++;
```

```solidity
File: src/ethereum-polygon/EthereumToPolygonRelayer.sol

56:     nonce++;
```

```solidity
File: src/libraries/CallLib.sol

61:     for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
```

- For each `nonce` instance, simply replaces with `unchecked{++nonce;}`.
- For the `CallLib` loop, change to:

```solidity=
for (uint256 _callIndex; _callIndex < _callsLength;) {
    // for loop content
    unchecked{++_callsLength;}
}
```

Saves about ~$40$ gas per unchecked operations. If call data's length is long, the gas saved will be significant.

Gas report for G-02 (not counting G-01): https://gist.github.com/midori-fuse/996c6e5f56b2678ed9acdc6ff01b1b80

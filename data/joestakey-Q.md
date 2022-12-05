## Summary
### Low Risk
|      | Issue                                                                                |
|------|--------------------------------------------------------------------------------------|
| L-01 | `EthereumToArbitrumRelayer` breaks `EIP5164`                                         |
| L-02 | `EthereumToArbitrumRelayer.processCalls()` does not check `msg.sender` is a contract |
| L-03 | `CallLib.executeCalls` should check for `_call.target` existence                     |


## Low
### [L‑01] `EthereumToArbitrumRelayer` breaks `EIP5164`


As detailed in `eip-5164`:
```
CrossChainRelayers MUST emit the RelayedCalls event when a batch of calls is relayed
```

This is also specified in `ICrossChainRelayer`, where it is said `RelayedCalls` should be emitted [when calls have been successfully relayed](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/interfaces/ICrossChainRelayer.sol#L21) to the executor chain.

But in the Arbitrum implementation, `RelayedCalls` is [emitted](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L84) in `relayCalls`. This function only writes transaction hash in storage, but does not actually relay anything to the executor chain.

Calls are relayed to the executor chain in `processCalls`, which does not emit `RelayedCalls`.

This breaks `eip-5164` and creates problem for off-chain tools tracking `RelayedCalls` events.

## Lines of code

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L84

## Mitigation

`RelayedCalls` should be emitted in `CrossChainRelayerArbitrum.processCalls`, not in `CrossChainRelayerArbitrum.relayCalls`.
But for clarity, and to keep consistency with `ICrossChainRelayer`:
- `CrossChainRelayerArbitrum.processCalls` should be renamed `CrossChainRelayerArbitrum.relayCalls`.
- `CrossChainRelayerArbitrum.relayCalls` should be renamed `startRelayCalls`.

### [L‑02] `EthereumToArbitrumRelayer.processCalls()` does not check `msg.sender` is a contract

The Arbitrum relay `processCalls` is intended to be called by EOA, as specified in the docs:
```
Arbitrum requires an EOA to submit a bridge transaction.
```

But the function does not actually perform any check to ensure `msg.sender` is an EOA.

This should be enforced not only because of what is specified in the docs, but also because a contract calling `EthereumToArbitrumRelayer.processCalls()` can lead to the gas refund being lost:

The `excessFeeRefundAddress` parameter of Arbitrum's `Inbox.createRetryableTicket()` [gets credited with the gas refund](https://github.com/OffchainLabs/arbitrum/blob/a1455576bc6adbd78a89c6c90b6f327c1013bb92/packages/arb-bridge-eth/contracts/bridge/Inbox.sol#L349) - if there is any.

In the `EthereumToArbitrumRelayer.processCalls()` implementation, that parameter [is set](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L122) to be `msg.sender`.

The issue is that if the caller is a contract, the refund will then be transferred to the L2 alias of the `msg.sender`, which:
- may not exist on Arbitrum
- may not have any withdrawal function

In such case, the refund fee is lost.

## Lines of code

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L122

## Mitigation

Add a `msg.sender == tx.origin` check in `processCalls` to ensure only EOAs can call the function.

### [L‑03] `CallLib.executeCalls` should check for `_call.target` existence

As per Solidity's documentation:
```
The low-level functions call, delegatecall and staticcall return true as their first return value if the account called is non-existent
```

`CallLib.executeCalls` does not check for `_call.target` existence, meaning if it has no bytecode, the function call would not revert.

## Lines of code

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L64

## Mitigation

Use `extcodesize()` in `executeCalls` to ensure `_call.target` has bytecode
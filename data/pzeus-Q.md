## Issue
There are no defined events when setting a new executor in `EthereumToArbitrumRelayer.sol` and `EthereumToOptimismRelayer.sol`
The same applies when setting a new relayer in `EthereumToArbitrumExecutor.sol` and `EthereumToOptimismExecutor.sol`

## Proof of Concept
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L139
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L66
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L85

## Recommendation
There should be a defined event, preferably named `event RelayerSet(address indexed relayer);` inside `EthereumToArbitrumExecutor.sol` and `EthereumToOptimismExecutor.sol` contracts. This event should be emitted right after setting the `relayer` address [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L54
) and [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L68
)

The same also applies to `EthereumToArbitrumRelayer.sol` and `EthereumToOptimismRelayer.sol`. Define `event ExecutorSet(address indexed executor);` which has to be emitted after setting the `executor` address [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L141
) and [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L87
)

## Issue
Function `getTxHash()` in `EthereumToArbitrumRelayer.sol` contract is exposed to be called externally. I do not see a reason why this is needed for the protocol to work properly. There is an internal `_getTxHash()` which is used inside the contract. The external `getTxHash()` basically just calls the internal one. This outside exposure is not needed in my opinion

## Proof of Concept
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L153

## Recommendation
Remove the external `getTxHash()` function from `EthereumToArbitrumRelayer.sol` contract
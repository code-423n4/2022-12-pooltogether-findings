There should be a defined event, preferably named `event RelayerSet(address indexed relayer);` inside `EthereumToArbitrumExecutor.sol` and `EthereumToOptimismExecutor.sol` contracts. This event should be emitted right after setting the `relayer` address [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L54
) and [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L68
)

The same also applies to `EthereumToArbitrumRelayer.sol` and `EthereumToOptimismRelayer.sol`. Define `event ExecutorSet(address indexed executor);` which has to be emitted after setting the `executor` address [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L141
) and [here](https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L87
)


## Proof of Concept
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L139
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L66
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L85
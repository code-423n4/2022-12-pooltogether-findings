**src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol **
- L16 - The contract is called CrossChainRelayerArbitrum, but the file is called EthereumToArbitrumRelayer.sol, this is confusing to recognize when looking for contracts, therefore it would be beneficial and easier for the contract and the file to be called the same.


**src/ethereum-arbitrum/CrossChainExecutorArbitrum.sol**
- L15 - The contract is called CrossChainExecutorArbitrum, but the file is called EthereumToArbitrumExecutor.sol, this is confusing to recognize when looking for contracts, therefore it would be beneficial and easier for the contract and the file to have the same name.


**src/ethereum-optimism/EthereumToOptimismRelayer.sol **
- L16 - The contract is called CrossChainRelayerOptimism, but the file is called EthereumToOptimismRelayer.sol, this is confusing to recognize when looking for contracts, therefore it would be beneficial and easier for the contract and the file to have the same name.


**src/ethereum-optimism/EthereumToOptimismExecutor.sol**
- L16 - The contract is called CrossChainExecutorOptimism, but the file is called EthereumToOptimismExecutor.sol, this is confusing to recognize when looking for contracts, therefore it would be beneficial and easier for the contract and the file to have the same name.


**src/ethereum-polygon/EthereumToPolygonRelayer.sol**
- L7 - The ICrossChainExecutor interface is imported, but it is never used, so it should be removed.


**src/ethereum-polygon/EthereumToPolygonExecutor.sol**
- L14 - The contract is called CrossChainExecutorPolygon, but the file is called EthereumToPolygonExecutor.sol, this is confusing to recognize when looking for contracts, therefore it would be beneficial and easier for the contract and the file to be called the same.


**src/libraries/CallLib.sol**
- L55/56 - The function executeCalls() from the CallLib library receives as input a bool(_executedNonce) and depending on whether it is true or false, it reverses the function or not.
This mechanism is used by all Executor contracts, but that logic shouldn't be in the library anyway, since it has no reason to know that logic.
The logic for depending on whether executed[_nonce] is true or not, should be in the Executors contracts.

# QA
# Low
## Important functions should emit events
### Impact
Important state variables should be tracked when changed for correct functioning and better reaction time + off-chain tracking
### Github Permalinks
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L54
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L141
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L87
### Mitigation
Add an event emission

# Informational
## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 120 character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length
### Github Permalinks 

https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L14
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L14
https://github.com/pooltogether/ERC5164/blob/a177c79017ca508fb52be629877f680fef3d78a2/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L14


### Mitigation
Reduce line length to less than 120 at least to improve maintainability and readability of the code 




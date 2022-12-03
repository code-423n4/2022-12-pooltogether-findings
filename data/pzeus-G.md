## Issue
A pre-increment approach can be used instead of post-increment inside the `for loop`
This will decrease the gas cost because `i++` is generally more expensive. It must increment a value and "return" the old value, so it may require holding two numbers in memory. 

## Proof of concept
https://github.com/pooltogether/ERC5164/blob/main/src/libraries/CallLib.sol#L61

## Recommendation
Use `++i` instead of `i++`

## Issue
A pre-increment approach can be used instead of post-increment for incrementing the `nonce`

## Proof of concept
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60
https://github.com/pooltogether/ERC5164/blob/main/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56
## Recommendation
Use `++nonce` instead of `nonce++`
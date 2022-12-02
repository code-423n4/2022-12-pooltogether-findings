# QA


## Non Critical

### Use named import
Use the named imports syntax to make clear what are you importing, example `import { CallLib } from "../libraries/CallLib.sol";`

You are not using named imports, on lines;
- (EthereumToArbitrumExecutor.sol#L7-L8)[https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L7-L8]
- (EthereumToArbitrumRelayer.sol#L9)[https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L9]
- (EthereumToOptimismExecutor.sol#L7-L8)[https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L7-L8]
- (EthereumToOptimismRelayer.sol#L9)[https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L9]
- (EthereumToPolygonExecutor.sol#L7)[https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L7]
- (EthereumToPolygonRelayer.sol#L9)[https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L9]


### Library should have open pragma

The library CallLib should have an open pragma [CallLib.sol#L3](https://github.com/pooltogether/ERC5164/blob/851d1fe82f301ebe0e35e6f1f98bf8113135a9d2/src/libraries/CallLib.sol#L3).
Recommend you to change `pragma solidity 0.8.16;` to `pragma solidity ^0.8.4;`

### Format code with a tool
Use a formatting tool to standarized code style, you could use for example `forge fmt`
https://book.getfoundry.sh/reference/config/formatter
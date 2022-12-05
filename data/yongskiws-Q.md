##QA

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L105
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L67
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L105
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L157
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L177

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L49

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L45

using smaller integer types such as `uint8` , `uint16`, or `uint32` can save on gas compared to using larger integer types like uint256. This is because smaller integer types take up less space on the blockchain, so they require less gas to be stored and processed.

However, it is important to note that using smaller integer types can also limit the range of values that can be stored and processed. For example, the uint8 type can only store values from 0 to 255, so it may not be suitable for all use cases. It is important to carefully consider the range of values that will be needed in your contract and choose the appropriate integer type to use.

The  uint256 `gasLimit` parameter is used to ensure that the relayer does not spend too much gas on executing the Call structs, as this could result in the transaction failing and the relayer not being paid for their services. Additionally, the gasLimit parameter can also be used to prevent the relayer from executing arbitrarily large or complex transactions that could potentially slow down .does it need to be changed to uint32 so that the cost is cheaper?



## function set `maxGasLimit` is not set in the contract in case of future problems
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L56-L62
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L33-L40
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L38-L44

function setGaslimit() external  {
  require(_maxGasLimit > 0, "Relayer/max-gas-limit-gt-zero");
  maxGasLimit = _maxGasLimit;
}
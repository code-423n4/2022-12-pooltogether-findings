# [L-01] nonce should be marked as public instead of internal.

### Line Of Code:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L29

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L23

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L40

### Vulnerability Detail and recommended fix

in the current implementation, the nonce is set to internal, and the user is only able to know the nonce by querying the blockchain transaction and read the emitted event log or use the return value from relayCall, which is very inconvenient to keep track of the nonce.

```solidity
/// @notice Nonce to uniquely idenfity each batch of calls.
uint256 internal nonce;
```

We recommend the codebase change the access control for nonce from internal to public.

# [L-02] unsafe downcasting of the gas limit from uint256 to uint32 in OptimismRelayer.sol

### Line Of Code:

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L72

### Vulnerability Detail and recommended fix

The gas limit is unsafely downcasted from uint256 to uint32 in OptimismRelayer.sol

```solidity
crossDomainMessenger.sendMessage(
  address(executor),
  abi.encodeWithSignature(
	"executeCalls(uint256,address,(address,bytes)[])",
	_nonce,
	msg.sender,
	_calls
  ),
  uint32(_gasLimit)
);
```

the gas limit can be truncated if the gas limit that is larger than uint32 is set.

We recommend the project use openzepplin unsafe case to make sure the gas limit is not trancunated sliently.
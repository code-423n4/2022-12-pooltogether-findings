## QA


### The order of the layout does not follow best practices, the event is coming before state variables

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

```solidity
file: ERC5164/src/ethereum-polygon/EthereumToPolygonExecutor.sol

  22: event ExecutedCalls(address indexed relayer, uint256 indexed nonce);

  31: mapping(uint256 => bool) public executed;
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L22

&nbsp;&nbsp;

```solidity
file: ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

  26  event ProcessedCalls(uint256 indexed nonce, address indexed sender, uint256 indexed ticketId);

  31  IInbox public immutable inbox;
```

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L26

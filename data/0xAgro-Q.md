# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
[NC-01]|Long Lines (> 120 Characters)|3|
[NC-02]|Inconsistent Function Brace Newline|2|
[NC-03]|Inconsistent `@notice` Tag Format|1|
[NC-04]|Non-NatSpec `///` Used|1|

### [NC-01] Long Lines (> 120 Characters)

Lines with greater length than 120 characters are used. The [Solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

#### Findings:

*/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol*
Links: [14](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L14).
```solidity
14:	 *         It lives on the Ethereum chain and communicates with the `CrossChainExecutorArbitrum` contract on the Arbitrum chain.
```

*/src/ethereum-optimism/EthereumToOptimismRelayer.sol*
Links: [14](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L14).
```solidity
14:	 *         It lives on the Ethereum chain and communicates with the `CrossChainExecutorOptimism` contract on the Optimism chain.
```

*/src/ethereum-polygon/EthereumToPolygonRelayer.sol*
Links: [14](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L14).
```solidity
14:	 *         It lives on the Ethereum chain and communicates with the `CrossChainExecutorPolygon` contract on the Polygon chain.
```

### [NC-02] Inconsistent Function Brace Newline

There are functions that start a brace on a newline and a majority that do not. Consider following a consistent function brace structure for better readability.

#### Findings:

*/src/ethereum-optimism/EthereumToOptimismRelayer.sol*
Links: [53](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L53).
```solidity
53:		{
```

*/src/ethereum-polygon/EthereumToPolygonRelayer.sol*
Links: [49](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L49).
```solidity
49:		{
```

### [NC-03] Inconsistent `@notice` Tag Format

Most `@notice` tags end with a period in the codebase. There are a few cases where  `@notice` tags do not end with a period.

#### Findings:

*/src/ethereum-optimism/EthereumToOptimismExecutor.sol*
Links: [25-26](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L25-L26).
```solidity
25:	* @notice Nonce to uniquely identify the batch of calls that were executed
26:	*         nonce => boolean
```

A similar `@notice` tag comment can be found [here](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L27) with a trailing period.

*/src/libraries/CallLib.sol*
Links: [13](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L13).
```solidity
13:	* @notice Call data structure
```

### [NC-04] Non-NatSpec `///` Used

The string `///` should only be used for NatSpec comments. According to the [Ethereum documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#natspec): "[NatSpec comments] are written with a triple slash (`///`) or a double asterisk block (`/** ... */`) and they should be used directly above function declarations or statements". `/// ...` will be interpreted as a `/// @notice` ([ref](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#tags)). Consider changing any `/// ...` to `/// @notice ...` or changing to a regular comment id applicable for better readability.

#### Findings:

*/src/ethereum-polygon/EthereumToPolygonRelayer.sol*
Links: [75](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L75).
```solidity
75:	/// no-op
```

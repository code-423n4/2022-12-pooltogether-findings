
**src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol **
- L58 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L57/58/109/140 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L57/140 - Use assembly to check for address(0), Saves 6 gas per instance.

- L78 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.


**src/ethereum-arbitrum/CrossChainExecutorArbitrum.sol**
- L53 - Use assembly to check for address(0), Saves 6 gas per instance.

- L53/65 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**src/ethereum-optimism/EthereumToOptimismRelayer.sol **
- L40 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L39/86 - Use assembly to check for address(0), Saves 6 gas per instance.

- L39/86 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L60 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.


**src/ethereum-optimism/EthereumToOptimismExecutor.sol**
- L38/67 - Use assembly to check for address(0), Saves 6 gas per instance.

- L38/67/80 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**src/ethereum-polygon/EthereumToPolygonRelayer.sol**
- L38 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L38 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L56 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.


**src/ethereum-polygon/EthereumToPolygonExecutor.sol**
- L54/57 - A variable is created in memory, but it is only used once, therefore the least expensive thing would be to directly use the storage variable on line 57.



**src/libraries/CallLib.sol**
- L61 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.

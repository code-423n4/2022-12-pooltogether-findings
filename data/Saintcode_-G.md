## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

Instances:
-EthereumToPolygonExecutor.sol line 47
-CallLib.sol line 52

## `++I` INSTEAD OF `I++` (OR USE ASSEMBLY WHEN APPLICABLE)

Use `++i` instead of ` i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. 

-EthereumToArbitrumRelayer.sol line 78
-EthereumToOptimismRelayer.sol line 60
.EthereumToPolygonRelayer.sol line 56
.CallLib.sol line 61


## USE MULTIPLE `REQUIRE()` STATMENTS INSTEAD OF `REQUIRE(EXPRESSION && EXPRESSION && ...)`

Instances:
-EthereumToOptimismExecutor.sol  line 80

## `>=` COSTS LESS GAS THAN `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

Instances:
-EthereumToOptimismRelayer.sol line 58
-EthereumToPolygonRelayer.sol line 38

## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
`
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}  `

Instances:
-CallLib.sol line 61

## USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Instances:
-EthereumToArbitrumExecutor.sol lines 53, 65
-EthereumToArbitrumRelayer.sol lines 57, 58, 109, 140
-EthereumToOptimismExecutor.sol lines 38, 67, 80
-EthereumToOptimismRelayer.sol lines 39, 40, 86
-EthereumToPolygonRelayer.sol line 38


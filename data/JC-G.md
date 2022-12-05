
## Summary

- G-01 Using calldata instead of memory for read-only arguments in external functions saves gas (1 instance)
- G-02 Splitting require() statements that use && saves gas (1 instance)
- G-03 abi.encode() is less efficient than abi.encodePacked() (1 instance)
- G-04 Empty blocks should be removed or emit something (1 instance)
- G-05 Use calldata instead of memory for function parameters (3 instances)

Total: 7 instances in 5 issues

---


## G-01 Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. 
Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

Instance (1):

libraries/CallLib.sol
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L52
     


## G-02 Splitting require() statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per &).

Instance (1):

EthereumToOptimismExecutor.sol
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol#L80-L84

 
## G-03 abi.encode() is less efficient than abi.encodePacked()

Instance (1):

EthereumToPolygonRelayer.sol
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L60


## G-04 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. 
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

Instance (1):

EthereumToPolygonRelayer.sol
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L74-L76
 


## G-05 Use calldata instead of memory for function parameters

Having function arguments use calldata instead of memory can save gas. 

Recommended Mitigation Steps: 
Change function arguments from memory to calldata.

Instance (3):

EthereumToPolygonRelayer.sol
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L74


EthereumToPolygonExecutor.sol
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonExecutor.sol#L47

CallLib.sol
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L52
 


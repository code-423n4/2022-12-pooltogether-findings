### Summary

Overall the codebase is quite readable, well organized and matched well with the specs described in the documenations. Project structure is intuitive and simple to follow. Contract structure is well ogranized and unifromally utilize static and a recent version of solidity (0.8.16). Variable naming is quite readble. Comments were used rigourously and were beneficial and accurate in describing the object they were comenting on. Events are used in a benefical manner to track protocol output and function across the entire process flow of EIP.
   
   

### Low Quality issues
#### [LQ-01] Variable not required

maxGasLimit is not used for Polygon's bridge and there is no free gas. Don't believe there is a use for the var unless you want to impose protocol level restrictions

instances:1

File: EthereumToPolgonRelayer
Line: 20
[Link](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol)

```javascript
  /// @notice Gas limit provided for free on Polygon.
  uint256 public immutable maxGasLimit;
```
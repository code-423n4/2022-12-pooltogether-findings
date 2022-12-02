#### Redundant payable
`CrossChainRelayerArbitrum`, `CrossChainRelayerOptimism` and `CrossChainRelayerPolygon` contracts uses `payable` on `relayCalls` function, which is redundant. This could lead to a funds lock, as users can send the native token to this function and there are no mechanisms for its withdrawal.


**Path:** ./ethereum-arbitrum/CrossChainRelayerArbitrum.sol : relayCalls();
./ethereum-optimism/CrossChainRelayerOptimism.sol : relayCalls();
./ethereum-polygon/CrossChainRelayerPolygon.sol : relayCalls()
**Recommendation:** Remove `payable` from the function.
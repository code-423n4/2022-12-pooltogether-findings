## Remove copying of `immutable` variables to `memory` 
In all 3 Relayer contracts, `maxGasLimit`, which is an `immutable` field is cached in `relayCalls` as `_maxGasLimit`, probably to save gas. 

It is unnecessary as `immutable` variables are not stored in `storage` but rather are directly added to the bytecode. So, `maxGasLimit` should be used directly. This also saves an insignificant 5 gas on each `relayCalls` call. 

## `executor`s can be declared of type `address` in Relayer contracts
Type of `executor` can be changed from `ICrossChainExecutor` to `address` as no calls are made using the interface.
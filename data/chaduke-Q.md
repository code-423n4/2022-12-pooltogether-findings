QA1: https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L52
It will be better to have a zero length check for ``_calls``.

QA2: https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol#L3
lock all files with the most recent version of Solidity 

QA3: https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L179
The correct implementation should be
```
return keccak256(abi.encode(address(this), _nonce, keccak256(_calls), _sender, _gasLimit));
```

QA4: https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L139
Need the protection of an access control or ``_executor`` should be set with a constructor. 

QA5: https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol#L52
``relayer`` should be set with a constructor or it needs the protection of an access control to avoid front running and wrong ``relayer`` is set.

QA6 :https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L85
``executor`` should be set with the constructor or it needs the protection of an access control to avoid front running in which wrong ``executor`` is set. 



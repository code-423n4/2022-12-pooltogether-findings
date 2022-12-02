

### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol::54 => relayer = _relayer;
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::60 => inbox = _inbox;
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::61 => maxGasLimit = _maxGasLimit;
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::141 => executor = _executor;
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::39 => crossDomainMessenger = _crossDomainMessenger;
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::68 => relayer = _relayer;
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::42 => crossDomainMessenger = _crossDomainMessenger;
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::43 => maxGasLimit = _maxGasLimit;
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::87 => executor = _executor;
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::39 => maxGasLimit = _maxGasLimit;
```



##### Non-Critical Issues



### [N01] Adding a return statement when the function defines a named return variable, is redundant



#### Findings:
```
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::86 => return _nonce;
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::131 => return _ticketID;
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::77 => return _nonce;
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::64 => return _nonce;
```



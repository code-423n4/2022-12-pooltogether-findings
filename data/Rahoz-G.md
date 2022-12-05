## Query storage data many time, should call one time to save gas
EthereumToArbitrumRelayer.relayCalls
EthereumToOptimismRelayer.relayCalls
EthereumToPolygonRelayer.relayCalls
It increase nonce by nonce++ and continue query that storage value in _nonce, it cost gas
### Proof of Concept
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78-L80
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60-L62
https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56-L58
### Recommended Mitigation Steps
By doing this way, we can increase storage value before query it to memory value _nonce.
uint256 _nonce = ++nonce;

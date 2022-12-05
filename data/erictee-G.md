### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
src/ethereum-polygon/EthereumToPolygonRelayer.sol:L60    _sendMessageToChild(abi.encode(_nonce, msg.sender, _calls));

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:L179    return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));

```

### [G-02] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/ethereum-polygon/EthereumToPolygonExecutor.sol:L39  constructor(address _fxChild) FxBaseChildTunnel(_fxChild) {}

```

### [G-03] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
src/ethereum-optimism/EthereumToOptimismExecutor.sol:L80    require(
```
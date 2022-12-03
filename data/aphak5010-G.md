# PoolTogether Gas Optimization Findings
## Summary
The Gas savings are calculated using the `yarn test` command.  
The same tests were used that are provided in the contest repository.

| Issue      | Title | File | Instances | Estimated Gas Savings (Deployments) | Estimated Gas Savings (Method calls) |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| G-00 | Calculate `nonce = nonce + 1` instead of `nonce++` | - | 3 | 3206 | 143 |
| G-01 | Redundant `getTxHash` function | EthereumToArbitrumRelayer.sol | 1 | 3006 | 60 |

## [G-00] Calculate `nonce = nonce + 1` instead of `nonce++`
There are 3 instances:  

EthereumToArbitrumRelayer:  

[https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L78)  

Deployment Gas saved: **1006**  
Method call Gas saved: **41**  

EthereumToOptimismRelayer:  

[https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L60)  

Deployment Gas saved: **1200**  
Method call Gas saved: **51**  

EthereumToPolygonRelayer:  

[https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-polygon/EthereumToPolygonRelayer.sol#L56)  

Deployment Gas saved: **1000**  
Method call Gas saved: **51**  

## [G-01] Redundant `getTxHash` function
In `EthereumToArbitrumRelayer.sol` there is an external `getTxHash` function and an internal `_getTxHash` function.  
You can use one public `getTxHash` function instead.  

```solidity
function getTxHash(
    uint256 _nonce,
    CallLib.Call[] calldata _calls,
    address _sender,
    uint256 _gasLimit
) public view returns (bytes32) {
    return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));
}
```

Deployment Gas saved: **3006**  
Method call Gas saved: **60**  

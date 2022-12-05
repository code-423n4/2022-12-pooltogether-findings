### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Hard-coding the `maxGasLimit` variable may cause problems in the future | 3 |
|[L-02]| Critical Address Changes Should Use Two-step Procedure | 4 |
|[L-03]|Initial value check is missing in Set Functions | 4 |
|[L-04]| Use `abi.encode()` to avoid collision | 1 |

Total 4 issues
### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01]|For functions, follow Solidity standard naming conventions|4|
| [NC-02] |Use a more recent version of Solidity |All contracts|
| [NC-03] |Solidity compiler optimizations can be problematic|  |
| [NC-04] |For modern and more readable code; update import usages| 6 |
| [NC-05] |`Use of bytes.concat() instead of abi.encodePacked(,) | 1|
| [NC-06] |Missing Event for critical parameters change | 4 |

Total 6 issues

### [L-01] Hard-coding the `maxGasLimit` variable may cause problems in the future

The variable `maxGasLimit` is defined as immutable and its value is assigned in `constructor` but cannot be changed later

EVM-Based blockchains are hardforked and there is no such thing as Gas Limit etc. values may change, this has happened in the past, so it is recommended to have this value updated in the future


```solidity

3 results - 3 files

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:
  36    /// @notice Gas limit provided for free on Arbitrum.
  37:   uint256 public immutable maxGasLimit;
  38  

src/ethereum-optimism/EthereumToOptimismRelayer.sol:
  25    /// @notice Gas limit provided for free on Optimism.
  26:   uint256 public immutable maxGasLimit;
  27  

src/ethereum-polygon/EthereumToPolygonRelayer.sol:
  19    /// @notice Gas limit provided for free on Polygon.
  20:   uint256 public immutable maxGasLimit;
```

### [L-02] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.


```js

4 results - 4 files

src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:
  51     */
  52:   function setRelayer(ICrossChainRelayer _relayer) external {
  53      require(address(relayer) == address(0), "Executor/relayer-already-set");

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:
  138     */
  139:   function setExecutor(ICrossChainExecutor _executor) external {
  140      require(address(executor) == address(0), "Relayer/executor-already-set");

src/ethereum-optimism/EthereumToOptimismExecutor.sol:
  65     */
  66:   function setRelayer(ICrossChainRelayer _relayer) external {
  67      require(address(relayer) == address(0), "Executor/relayer-already-set");

src/ethereum-optimism/EthereumToOptimismRelayer.sol:
  84     */
  85:   function setExecutor(ICrossChainExecutor _executor) external {
  86      require(address(executor) == address(0), "Relayer/executor-already-set");

```

**Recommended Mitigation Steps:**
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

### [L-03] Initial value check is missing in Set Functions

**Context:**
```js
4 results - 4 files

src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:
  51     */
  52:   function setRelayer(ICrossChainRelayer _relayer) external {
  53      require(address(relayer) == address(0), "Executor/relayer-already-set");

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:
  138     */
  139:   function setExecutor(ICrossChainExecutor _executor) external {
  140      require(address(executor) == address(0), "Relayer/executor-already-set");

src/ethereum-optimism/EthereumToOptimismExecutor.sol:
  65     */
  66:   function setRelayer(ICrossChainRelayer _relayer) external {
  67      require(address(relayer) == address(0), "Executor/relayer-already-set");

src/ethereum-optimism/EthereumToOptimismRelayer.sol:
  84     */
  85:   function setExecutor(ICrossChainExecutor _executor) external {
  86      require(address(executor) == address(0), "Relayer/executor-already-set");

```
Checking whether the current value and the new value are the same should be added


### [L-04] Use `abi.encode()` to avoid collision 

**Context:**

```solidity
src/libraries/CallLib.sol:
  64        (bool _success, bytes memory _returnData) = _call.target.call(
  65:         abi.encodePacked(_call.data, _nonce, _sender)
  66        );

```
**Description:**
If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of a into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c"). If you use abi.encodePacked for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, abi.encode should be preferred.

_**When are these used?**_
```abi.encode:```
abi.encode encode its parameters using the ABI specs. The ABI was designed to make calls to contracts.
Parameters are padded to 32 bytes. If you are making calls to a contract you likely have to use abi.encode
If you are dealing with more than one dynamic data type as it prevents collision.

```abi.encodePacked:```
abi.encodePacked encode its parameters using the minimal space required by the type. Encoding an uint8 it will use 1 byte. It is used when you want to save some space, and not calling a contract.Takes all types of data and any amount of input.

**Proof of Concept:**
```js
contract Contract0 {

    // abi.encode
    // (AAA,BBB) keccak = 0xd6da8b03238087e6936e7b951b58424ff2783accb08af423b2b9a71cb02bd18b
    // (AA,ABBB) keccak = 0x54bc5894818c61a0ab427d51905bc936ae11a8111a8b373e53c8a34720957abb
    function collision(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encode(_text, _anotherText));
    }
}

contract Contract1 {

    // abi.encodePacked
    // (AAA,BBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358
    // (AA,ABBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358

    function hard(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encodePacked(_text, _anotherText));
    }
}
```


### [NC-01] For functions, follow Solidity standard naming conventions

**Context:**

```solidity

4 results - 4 files

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:
  40:   uint256 internal nonce;

src/ethereum-optimism/EthereumToOptimismRelayer.sol:
  29:   uint256 internal nonce;

src/ethereum-polygon/EthereumToPolygonRelayer.sol:
  23:   uint256 internal nonce;

src/libraries/CallLib.sol:
  48     */
  49:   function executeCalls(
  50:     uint256 _nonce,
  51:     address _sender,
  52:     Call[] memory _calls,
  53:     bool _executedNonce
  54:   ) internal {

```

The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

### [NC-02] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used , newer version can be used `(0.8.17)`



### [NC-03] Solidity compiler optimizations can be problematic

```js
hardhat.config.ts:
  51    },
  52:   solidity: {
  53:     version: '0.8.16',
  54:     settings: {
  55:       optimizer: {
  56:         enabled: true,
  57:         runs: 2000,

```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.


**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [NC-04] For modern and more readable code; update import usages

**Context:**

```solidity
6 results - 5 files

src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:
  7: import "../interfaces/ICrossChainExecutor.sol";
  8: import "../libraries/CallLib.sol";

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:
  9: import "../libraries/CallLib.sol";

src/ethereum-optimism/EthereumToOptimismRelayer.sol:
  9: import "../libraries/CallLib.sol";

src/ethereum-polygon/EthereumToPolygonExecutor.sol:
  7: import "../libraries/CallLib.sol";

src/ethereum-polygon/EthereumToPolygonRelayer.sol:
  9: import "../libraries/CallLib.sol";

```


**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```
### [NC-05] Use of bytes.concat() instead of abi.encodePacked(,)

```solidity

src/libraries/CallLib.sol:
  62        Call memory _call = _calls[_callIndex];
  63: 
  64:       (bool _success, bytes memory _returnData) = _call.target.call(
  65:         abi.encodePacked(_call.data, _nonce, _sender)
  66:       );

```
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, bytes.concat() is enabled

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)

### [NC-06] Missing Event for critical parameters change

```solidity
src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol:
  51     */
  52:   function setRelayer(ICrossChainRelayer _relayer) external {
  53:     require(address(relayer) == address(0), "Executor/relayer-already-set");
  54:     relayer = _relayer;
  55:   }
  56  

src/ethereum-optimism/EthereumToOptimismExecutor.sol:
  65     */
  66:   function setRelayer(ICrossChainRelayer _relayer) external {
  67:     require(address(relayer) == address(0), "Executor/relayer-already-set");
  68:     relayer = _relayer;
  69:   }
  70 

src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol:
  138     */
  139:   function setExecutor(ICrossChainExecutor _executor) external {
  140:     require(address(executor) == address(0), "Relayer/executor-already-set");
  141:     executor = _executor;
  142:   }
  143  

src/ethereum-optimism/EthereumToOptimismRelayer.sol:
  84     */
  85:   function setExecutor(ICrossChainExecutor _executor) external {
  86:     require(address(executor) == address(0), "Relayer/executor-already-set");
  87:     executor = _executor;
  88:   }
  89  }

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit

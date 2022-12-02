- [Low](#low)
    - [**1. EIP with failures**](#1-eip-with-failures)
    - [**2. Integer overflow by unsafe casting**](#2-integer-overflow-by-unsafe-casting)
- [Non critical](#non-critical)
    - [**3. Outdated compiler**](#3-outdated-compiler)

# Low

## **1. EIP with failures**

The `EIP-5164` standard contains design flaws that prevent executing different orders with different `msg.value`, this standard should define the value with which each of the contracts is called, otherwise it is not flexible enough.

```diff
struct Call {
  address target;
+ uint value;
  bytes data;
}
```

**Reference:**

- https://eips.ethereum.org/EIPS/eip-5164

## **2. Integer overflow by unsafe casting**

Since the `_gasLimit` value is later casted in an unsafe way, it is necessary to check when setting that this value is less than the range allowed by `uint32`.

Keep in mind that the version of solidity used, despite being greater than `0.8`, does not prevent integer overflows during casting, it only does so in mathematical operations.

```javascript
    crossDomainMessenger.sendMessage(
      address(executor),
      abi.encodeWithSignature(
        "executeCalls(uint256,address,(address,bytes)[])",
        _nonce,
        msg.sender,
        _calls
      ),
      uint32(_gasLimit)
    );
```

It is necessary to safely convert between the different numeric types.

**Recommendation:**

Use a [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) from Open Zeppelin.

**Affected source code:**

- [EthereumToOptimismRelayer.sol:40](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L40)
- [EthereumToOptimismRelayer.sol:72](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol#L72)

---

# Non critical

## **3. Outdated compiler**

The pragma version used are:

```
pragma solidity 0.8.16;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.17](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)

- Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

Apart from these, there are several minor bug fixes and improvements.

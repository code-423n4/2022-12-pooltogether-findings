
### 1st BUG
Don't Initialize Variables with Default Value

#### Impact
Issue Information: 
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
ERC5164/script/helpers/DeployedContracts.sol::48 => for (uint256 i = 0; i < filesName.length; i++) {
```
#### Tools used
Manual






### 2nd BUG
Cache Array Length Outside of Loop

#### Impact
Issue Information: Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
ERC5164/script/helpers/DeployedContracts.sol::48 => for (uint256 i = 0; i < filesName.length; i++) {
ERC5164/script/helpers/DeployedContracts.sol::62 => uint256 filesNameLength = filesName.length;
ERC5164/src/libraries/CallLib.sol::59 => uint256 _callsLength = _calls.length;
```
#### Tools used
Manual



### 3rd BUG
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
ERC5164/script/helpers/DeployedContracts.sol::74 => keccak256(abi.encodePacked((contractName))) == keccak256(abi.encodePacked((_contractName)))
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::179 => return keccak256(abi.encode(address(this), _nonce, _calls, _sender, _gasLimit));
```
#### Tools used
Manual






### 4th BUG
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
ERC5164/script/bridge/BridgeToMumbai.s.sol::9 => import { ICrossChainRelayer } from "../../src/interfaces/ICrossChainRelayer.sol";
ERC5164/script/bridge/BridgeToMumbai.s.sol::10 => import { CrossChainRelayerPolygon } from "../../src/ethereum-polygon/EthereumToPolygonRelayer.sol";
ERC5164/script/bridge/BridgeToMumbai.s.sol::22 => data: abi.encodeWithSignature("setGreeting(string)", "Hello from L1")
ERC5164/script/bridge/BridgeToOptimismGoerli.s.sol::7 => import { ICrossDomainMessenger as IOptimismBridge } from "@eth-optimism/contracts/libraries/bridge/ICrossDomainMessenger.sol";
ERC5164/script/bridge/BridgeToOptimismGoerli.s.sol::10 => import { ICrossChainRelayer } from "../../src/interfaces/ICrossChainRelayer.sol";
ERC5164/script/bridge/BridgeToOptimismGoerli.s.sol::11 => import { CrossChainRelayerOptimism } from "../../src/ethereum-optimism/EthereumToOptimismRelayer.sol";
ERC5164/script/bridge/BridgeToOptimismGoerli.s.sol::23 => data: abi.encodeWithSignature("setGreeting(string)", "Hello from L1")
ERC5164/script/deploy/DeployToArbitrumGoerli.s.sol::6 => import { IInbox } from "@arbitrum/nitro-contracts/src/bridge/IInbox.sol";
ERC5164/script/deploy/DeployToArbitrumGoerli.s.sol::9 => import { CrossChainExecutorArbitrum } from "../../src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol";
ERC5164/script/deploy/DeployToArbitrumGoerli.s.sol::10 => import { CrossChainRelayerArbitrum } from "../../src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol";
ERC5164/script/deploy/DeployToMumbai.s.sol::8 => import { CrossChainExecutorPolygon } from "../../src/ethereum-polygon/EthereumToPolygonExecutor.sol";
ERC5164/script/deploy/DeployToMumbai.s.sol::9 => import { CrossChainRelayerPolygon } from "../../src/ethereum-polygon/EthereumToPolygonRelayer.sol";
ERC5164/script/deploy/DeployToOptimismGoerli.s.sol::6 => import { ICrossDomainMessenger } from "@eth-optimism/contracts/libraries/bridge/ICrossDomainMessenger.sol";
ERC5164/script/deploy/DeployToOptimismGoerli.s.sol::9 => import { CrossChainExecutorOptimism } from "../../src/ethereum-optimism/EthereumToOptimismExecutor.sol";
ERC5164/script/deploy/DeployToOptimismGoerli.s.sol::10 => import { CrossChainRelayerOptimism } from "../../src/ethereum-optimism/EthereumToOptimismRelayer.sol";
ERC5164/script/helpers/DeployedContracts.sol::9 => import { CrossChainRelayerOptimism } from "../../src/ethereum-optimism/EthereumToOptimismRelayer.sol";
ERC5164/script/helpers/DeployedContracts.sol::10 => import { CrossChainExecutorOptimism } from "../../src/ethereum-optimism/EthereumToOptimismExecutor.sol";
ERC5164/script/helpers/DeployedContracts.sol::12 => import { CrossChainRelayerPolygon } from "../../src/ethereum-polygon/EthereumToPolygonRelayer.sol";
ERC5164/script/helpers/DeployedContracts.sol::13 => import { CrossChainExecutorPolygon } from "../../src/ethereum-polygon/EthereumToPolygonExecutor.sol";
ERC5164/script/helpers/DeployedContracts.sol::15 => import { CrossChainRelayerArbitrum } from "../../src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol";
ERC5164/script/helpers/DeployedContracts.sol::16 => import { CrossChainExecutorArbitrum } from "../../src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol";
ERC5164/script/helpers/DeployedContracts.sol::20 => string constant OP_GOERLI_PATH = "/broadcast/DeployToOptimismGoerli.s.sol/420/";
ERC5164/script/helpers/DeployedContracts.sol::21 => string constant MUMBAI_PATH = "/broadcast/DeployToMumbai.s.sol/80001/";
ERC5164/script/helpers/DeployedContracts.sol::22 => string constant ARBITRUM_PATH = "/broadcast/DeployToArbitrumGoerli.s.sol/421613/";
ERC5164/script/helpers/DeployedContracts.sol::92 => "/broadcast/DeployToOptimismGoerli.s.sol/5/",
ERC5164/script/helpers/DeployedContracts.sol::101 => _getContractAddress("CrossChainExecutorOptimism", OP_GOERLI_PATH, "executor-not-found")
ERC5164/script/helpers/DeployedContracts.sol::106 => return Greeter(_getContractAddress("Greeter", OP_GOERLI_PATH, "greeter-not-found"));
ERC5164/script/helpers/DeployedContracts.sol::115 => "/broadcast/DeployToMumbai.s.sol/5/",
ERC5164/script/helpers/DeployedContracts.sol::124 => _getContractAddress("CrossChainExecutorPolygon", MUMBAI_PATH, "executor-not-found")
ERC5164/script/helpers/DeployedContracts.sol::129 => return Greeter(_getContractAddress("Greeter", MUMBAI_PATH, "greeter-not-found"));
ERC5164/script/helpers/DeployedContracts.sol::138 => "/broadcast/DeployToArbitrumGoerli.s.sol/5/",
ERC5164/script/helpers/DeployedContracts.sol::147 => _getContractAddress("CrossChainExecutorArbitrum", ARBITRUM_PATH, "executor-not-found")
ERC5164/script/helpers/DeployedContracts.sol::152 => return Greeter(_getContractAddress("Greeter", ARBITRUM_PATH, "greeter-not-found"));
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol::5 => import { AddressAliasHelper } from "@arbitrum/nitro-contracts/src/libraries/AddressAliasHelper.sol";
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumExecutor.sol::7 => import "../interfaces/ICrossChainExecutor.sol";
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::5 => import { IInbox } from "@arbitrum/nitro-contracts/src/bridge/IInbox.sol";
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::7 => import { ICrossChainExecutor } from "../interfaces/ICrossChainExecutor.sol";
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::8 => import { ICrossChainRelayer } from "../interfaces/ICrossChainRelayer.sol";
ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol::112 => "executeCalls(uint256,address,(address,bytes)[])",
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::5 => import { ICrossDomainMessenger } from "@eth-optimism/contracts/libraries/bridge/ICrossDomainMessenger.sol";
ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol::7 => import "../interfaces/ICrossChainExecutor.sol";
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::5 => import { ICrossDomainMessenger } from "@eth-optimism/contracts/libraries/bridge/ICrossDomainMessenger.sol";
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::7 => import { ICrossChainExecutor } from "../interfaces/ICrossChainExecutor.sol";
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::8 => import { ICrossChainRelayer } from "../interfaces/ICrossChainRelayer.sol";
ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol::67 => "executeCalls(uint256,address,(address,bytes)[])",
ERC5164/src/ethereum-polygon/EthereumToPolygonExecutor.sol::5 => import { FxBaseChildTunnel } from "@maticnetwork/fx-portal/contracts/tunnel/FxBaseChildTunnel.sol";
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::5 => import { FxBaseRootTunnel } from "@maticnetwork/fx-portal/contracts/tunnel/FxBaseRootTunnel.sol";
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::7 => import { ICrossChainExecutor } from "../interfaces/ICrossChainExecutor.sol";
ERC5164/src/ethereum-polygon/EthereumToPolygonRelayer.sol::8 => import { ICrossChainRelayer } from "../interfaces/ICrossChainRelayer.sol";
```
#### Tools used
Manual




### 5th BUG
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
ERC5164/script/helpers/DeployedContracts.sol::20 => string constant OP_GOERLI_PATH = "/broadcast/DeployToOptimismGoerli.s.sol/420/";
ERC5164/script/helpers/DeployedContracts.sol::21 => string constant MUMBAI_PATH = "/broadcast/DeployToMumbai.s.sol/80001/";
ERC5164/script/helpers/DeployedContracts.sol::22 => string constant ARBITRUM_PATH = "/broadcast/DeployToArbitrumGoerli.s.sol/421613/";
```
#### Tools used
Manual




### Title: 
Potential security issues with CrossChainRelayerArbitrum contract

#### Description:
The CrossChainRelayerArbitrum contract (located at [contract_address]) contains several potential security issues that could allow attackers to manipulate the contract and cause it to run out of gas or manipulate the state of the Arbitrum chain.

#### Steps to reproduce:

At https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L67    

Call the relayCalls function from any contract or user, passing in a large array of calls and a high _gasLimit value.

Expected result:
The contract should check that the caller is the expected relay contract, and that the length of the _calls array and the _gasLimit parameter are within reasonable limits.

Actual result:
The contract does not check the caller or the length of the _calls array, and it does not check the _gasLimit parameter. This could allow attackers to exhaust the gas limit of the contract and cause it to run out of gas.

Similar issues can be found in the processCalls function, where the caller and _calls array length are not checked, and the _maxSubmissionCost parameter is not checked.

#### Impact:
These issues could potentially allow attackers to manipulate the contract and cause it to run out of gas or manipulate the state of the Arbitrum chain.

#### Solution:
Add checks to the relayCalls and processCalls functions to verify that the caller is the expected relay contract, and that the length of the _calls array and the _gasLimit and _maxSubmissionCost parameters are within reasonable limits. This will prevent attackers from using these functions to exhaust the gas limit of the contract or manipulate the state of the Arbitrum chain.





### Title: 
Security issues in CrossChainRelayerOptimism contract

### Description:
At https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismRelayer.sol

The CrossChainRelayerOptimism contract has several potential security issues that need to be addressed.

    The maxGasLimit parameter passed to the contract's constructor is not checked for validity. It is possible for an attacker to pass a very large gas limit, which could potentially lead to a denial of service attack if the contract runs out of gas when processing requests.

    The relayCalls function accepts a _gasLimit parameter, but this parameter is not checked for validity. An attacker could potentially pass a very large gas limit, which could cause the contract to run out of gas when processing the request.

    The setExecutor function does not check that the caller has the appropriate permissions before setting the executor address. This could potentially allow any caller to set the executor address, which could lead to security vulnerabilities if the caller is not authorized to do so.

### Steps to reproduce:

    Deploy the CrossChainRelayerOptimism contract on the blockchain.
    Call the setExecutor function from any address to set the executor address.
    Call the relayCalls function with a very large _gasLimit parameter.

### Expected behavior:

    The maxGasLimit parameter passed to the contract's constructor should be checked for validity and the contract should not accept a very large gas limit.
    The relayCalls function should check the _gasLimit parameter for validity and should not accept a very large gas limit.
    The setExecutor function should check that the caller has the appropriate permissions before setting the executor address.

### Actual behavior:

    The maxGasLimit parameter is not checked for validity and the contract accepts a very large gas limit.
    The relayCalls function does not check the _gasLimit parameter for validity and accepts a very large gas limit.
    The setExecutor function does not check that the caller has the appropriate permissions and allows any caller to set the executor address.

### Impact:

These security issues could potentially lead to a denial of service attack if an attacker is able to pass a very large gas limit to the contract, and could also allow unauthorized users to set the executor address, which could lead to further security vulnerabilities. It is recommended to address these issues before deploying the contract on the blockchain.
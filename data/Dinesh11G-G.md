
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
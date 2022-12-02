## 

## [GAS-1]   ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW

  
         File: ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

        78:   nonce++;

[Link To Code:  EthereumToArbitrumRelayer.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol)
        
        File:  ERC5164/src/ethereum-optimism/EthereumToOptimismRelayer.sol

       60 :   nonce++;

       FILE:  ERC5164/src/libraries/CallLib.sol

       61:     for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {

##

##  [GAS-2]   Instead of using operator && on single require or if checks . using double REQUIRE OR IF checks can save more gas. 

        FILE:    ERC5164/src/ethereum-optimism/EthereumToOptimismExecutor.sol

       80:  require(
             msg.sender == address(_crossDomainMessenger) &&
             _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
            "Executor/sender-unauthorized"
             );

        
[Link To Code:  EthereumToOptimismExecutor.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-optimism/EthereumToOptimismExecutor.sol)

##

##   [GAS-3]  STORAGE POINTER TO A STRUCTURE IS CHEAPER THAN COPYING EACH VALUE OF THE STRUCTURE INTO MEMORY, SAME FOR ARRAY AND MAPPING (1 INSTANCE)

             FILE:  ERC5164/src/libraries/CallLib.sol

            62:   Call memory _call = _calls[_callIndex];


[Link To Code:  CallLib.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/libraries/CallLib.sol)

  









































ERC5164/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol

[Link To Code:  EthereumToArbitrumRelayer.sol](https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol)





Issue	Instances
GAS-1	Use assembly to check for address(0)	7
GAS-2	Using bools for storage incurs overhead	4
GAS-3	Use Custom Errors	11
GAS-4	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	4
GAS-5	Use != 0 instead of > 0 for unsigned integer comparison	3
GAS-6	internal functions not called by the contract should be removed	1
1)++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops

File:ERC5164\src\libraries\CallLib.sol
  61,5:     for (uint256 _callIndex; _callIndex < _callsLength; _callIndex++) {
  
2)Splitting require() statements that use && saves gas

File:ERC5164\src\ethereum-optimism\EthereumToOptimismExecutor.sol

 80:        require(
 81:       msg.sender == address(_crossDomainMessenger) && 
 82:       _crossDomainMessenger.xDomainMessageSender() == address(_relayer),
            "Executor/sender-unauthorized"
             );
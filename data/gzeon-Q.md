## Low

### User who call processCalls in EthereumToArbitrumRelayer may cancel the retryable ticket

The `beneficiary/callValueRefundAddress` of retryable ticket is set to msg.sender, which can cancel the retryable. It is better to set it to address(this).

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L123

https://developer.offchainlabs.com/arbos/l1-to-l2-messaging#submitting-a-retryable
> A Retryable's beneficiary has the unique power to cancel the Retryable. This has the same effect as the Retryable timing out.

## Non-Critical

### Arbitrum createRetryableTicket do not accept gasLimit == 1

Here we check if _maxGasLimit > 0

https://github.com/pooltogether/ERC5164/blob/5647bd84f2a6d1a37f41394874d567e45a97bf48/src/ethereum-arbitrum/EthereumToArbitrumRelayer.sol#L58

but Arbitrum Inbox.createRetryableTicket also do not accept gasLimit == 1

https://github.com/OffchainLabs/nitro/blob/2c6e1359eb81e7af2986d334650c160ab21a4251/contracts/src/bridge/IInbox.sol#L126

>      * @dev Gas limit and maxFeePerGas should not be set to 1 as that is used to trigger the RetryableData error
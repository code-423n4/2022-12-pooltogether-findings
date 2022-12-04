## Title 
Security Analysis of CrossChainExecutorOptimism and CrossChainRelayerOptimism Contracts

## Impact

Implementing these recommendations will improve the security of the CrossChainExecutorOptimism and CrossChainRelayerOptimism contracts by preventing potential attackers from setting the relayer or executor addresses to malicious contracts, and by limiting the gas usage of calls executed by the contracts. This will help ensure the integrity and reliability of the communication between the Ethereum and Optimism chains.

## Summary

The CrossChainExecutorOptimism and CrossChainRelayerOptimism contracts are designed to enable communication between the Ethereum and Optimism chains. The CrossChainExecutorOptimism contract receives calls from the CrossChainRelayerOptimism contract on the Ethereum chain and executes them on the Optimism chain.

Upon review of the code, we have identified the following potential vulnerabilities:

The `setRelayer` function of the CrossChainExecutorOptimism contract can be called multiple times, potentially allowing an attacker to set the `relayer` address to a malicious contract.
The `setExecutor` function of the CrossChainRelayerOptimism contract can be called multiple times, potentially allowing an attacker to set the `executor` address to a malicious contract.
The `executeCalls` function of the CrossChainExecutorOptimism contract does not check the gas limit of the calls it receives, potentially allowing an attacker to use excessive gas and cause a denial of service attack.

## Recommendations

To address these vulnerabilities, we recommend the following changes to the contract code:

Implement a check to ensure that the `setRelayer` and `setExecutor` functions can only be called once. This can be done by storing a boolean value to indicate whether the relayer or executor address has already been set, and reverting the transaction if it has already been set.
Implement a check to ensure that the gas limit of calls received by the `executeCalls` function does not exceed the provided `maxGasLimit`. This can be done by comparing the provided gas limit with the `maxGasLimit` value and reverting the transaction if the gas limit is too high.

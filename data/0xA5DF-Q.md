## `relayCalls()` doesn't use `msg.value`, and user might send it ETH by mistake
On all 3 chains, the `relayCalls()` is payable but doesn't actually use the value.
Users might send it ETH by mistake, therefore it might be a good idea to check if any value is being sent and refund it in that case (that doesn't violate the specifications, and shouldn't cause much gas when `msg.value` is zero).

## Arbitrum implementation diverts from the EIP
For the Arbitrum implementation:
* Requires an additional call besides `relayCalls()`
* Allows a failed execution to have multiple attempts

Both of those are not inline with EIP-5164, and therefore it should be made clear that it's not exactly an implementation of the standard.

## Polygon gas limit isn't enforced
This seems insignificant, but it might be a good idea to actually enforce that by sending the gas limit to the dest chain and enforce it in order to meet the standard and users' expectations.
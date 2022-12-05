# Make `relayer` immutable

Since relayer can only be set once via `setRelayer`, this concept is similar to `immutable`, and this immutable will get directly embedded in bytecode, saving SLOAD. 

In order for `relayer` to be immutable, the `relayer` need to be set inside constructor, and remove the `setRelayer`.

If we think about it again, the `setRelayer` can only be called once when the relayer is not set (address 0), and the `executeCalls` will revert if the relayer is not set (because of `_isAuthorized`). There are no other function inside `Executor` contracts beside `executeCalls` therefore we can safely remove the `setRelayer` and set the `relayer` inside constructor with no changes made to logic.

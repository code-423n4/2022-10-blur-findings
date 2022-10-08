# Low Severity Issues

## [L-1] Missing zero address checks
`BlurExchange.initialize` doesn't check for zero address for `_weth`, `_executionDelegate`, `_policyManager`, and `_oracle`.

## [L-2] ERC-20 `transferFrom` value not checked
`transferFrom` return value, which is then returned by `ExecutionDelegate.transferERC20` is not checked to be `true` in `BlurExchange._transferTo`. Some tokens like ZRX, return `false` instead of reverting. Since the contracts are likely to use only WETH, this should not affect the proper functioning. 

## [L-3] `ExecutionDelegate.transferERC20` may fail for non-standard tokens
`ExecutionDelegate.transferERC20` assumes that `transferFrom` will always return a `bool` value. Tokens like USDT don't return a `bool`, so `transferERC20` will be reverted. Consider using a low-level call for `transferFrom` and returning the success status if it is necessary to return a `bool`. It will not be an issue if only WETH is used, which follows the standard.

##  [L-4] Transfer of zero tokens
Few tokens choose to revert when trying to transfer zero tokens. When using those tokens, the transaction may get reverted in `_transferFees` if any recipient gets zero tokens. WETH doesn't revert on zero token transfer. To be on the safe side, consider checking if amount > 0 before transfers.

## [L-5] Centralization of privileges
The contracts use `ownerOnly` modifier for many crucial setter functions and can break the protocol if someone gets the access to the owner wallet. Consider using a multi-sig wallet or making a DAO the owner to decrease the risks.

## [L-6] Use `type(uint256).max` for `Order.expirationTime` for oracle cancellations
Currently orders with expirationTime set to 0 are settleable at any time subject to fulfillment of other conditions. Since by default it is set to 0 only, missing initialization will cause issues. It is suggested to set it to `type(uint256).max` explicitly, for oracle cancellations to prevent this.

# Non-Critical

## [Q-1] `pragma abicoder v2` may be omitted
Abicoder v2 is enabled by default in Solidity 0.8.0 and above.

## [Q-2] Use `block.chainid` to access the chain ID
In `BlurExchange.initialize`, it is recommended to use `block.chainid` instead of passing it as a parameter. It reduces risk of manual error and saves about 700 gas.

## [Q-3] Missing revert reason
Revert reason is not specified in `BlurExchange.execute` 
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134

## [Q-4] `BlurExchange.cancelOrder` should revert if fails
A user cannot easily know if `BlurExchange.cancelOrder` failed without noticing a missing log. It is recommended to revert with a suitable message on failing. To ensure proper functioning of `cancelOrders` , the core logic can be extracted to an internal function that returns `bool` for success status, which can be used to revert in `cancelOrder` if it returns `false`, or can be ignored in `cancelOrders`. 

## [Q-5] Comparison with boolean constants
There are 5 instances in `ExecutionDelegate.sol` and `BlurExchange.sol` where there is an unnecessary comparison with boolean constants. Boolean variables need not be explicitly compared using a `==` operator. An example of this is:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77

## [Q-6] Unused variable `merklePath` in `BlurExchange._validateOracleAuthorization`
Unused variable at: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388. Also saves 400 gas at deployment.

## [Q-7] Use appropriate names for variables
`ExecutionDelegate.contracts` should be renamed to something like `contractsApproved` to better reflect the use.

## [Q-8] Unnecessary use of `payable`
`OrderStructs.Fee.recipient` is declared of type `address payable` even though it is explicitly converted to `payable` in `BlurExchange._transferTo`. `payable` should be removed to increase consistency across other address members in other structs.

## [Q-9] Use checks-effects-interaction pattern
While there is no risk due to the usage of a reentrancy-guard, `BlurExchange.execute` updates `cancelledOrFilled` after potentially dangerous external calls, which is not recommended practice. Consider moving these state changes above the `_execute*transfer` calls.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L164-165
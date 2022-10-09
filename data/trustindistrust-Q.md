## Non-Critical: ERC20 token transfer should use `safeTransferFrom` or check the return values

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L119)

❗Normally this would be higher severity, however this codepath is only activated on the transfer of WETH from a known contract. Since the behavior can be assumed, this reduces the severity. That said, it's still good defense-in-depth to use safer methods. The exchange may also open itself to other ERC20 tokens as acceptable trade tokens later, where this would become more important.

It is best practice to use `safeTransferFrom` or manually check the return values from the transfer of ERC20 tokens.

## Non-critical: `transferERC20` should be renamed to be `transferERC20Unsafe` to maintain consistency with other naming

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L119)

Maintain consistent function naming with `transferERC721Unsafe`, which also doesn't use a `safeTransferFrom` function in its implementation. If the implementation is changed as suggested in another finding, this finding can be ignored.

## Non-critical: Remove `transferERC721Unsafe` or implement manual checks in its implementation.

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L73)

The reason for inclusion of this function in the contracts is unclear. It isn't called by any contract as submitted, and a better alternative exists in `transferERC721`, which uses `safeTransferFrom`.

If the function does have a purpose for the protocol, it should implement internal checks on the result of `transferFrom`. This is especially important as the ERC721 tokens being transferred are user-supplied and thus cannot be trusted. Such checks would include a call to the token's ownership method to see if ownership truly changed.

## Non-critical: Remove `return` statement from `_canMatchOrders` as it is unnecessary

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L433)

`_canMatchOrders` uses named return values, and contains a branch that ensures that the named return values can't be shadowed. As such, the final `return` statement is unnecessary and can be removed.

Warden removed the statement and ran all tests. Tests passed.

## Non-critical: Move fee accumulator above the transfer of tokens in `_transferFee`

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L479)

While there is no risk of malicious effects, it is best practice to move changes to state or scoped state above side effects like transfers ("Checks Effects Interactions" pattern).

## Low: Sellers can grief by setting large Fee arrays with `0` for the value, causing `execute` to consume more gas than expected to process trades.

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

#### Description

When a sell order to created, it includes provision for a dynamic `Fee` array. This struct is designed to allow the seller to indicate the amount and destination for fees owed during a trade.

The loop that processes the trades has a cap of 255 such fee entries. As such, a seller that wanted to grief buyers could create and sign a sell `Order` with an array of at least 256 members with the `rate` set to 0. This would cause the computed fees to always be 0, which is a quantity that would likely be displayed in any UX associated with the contract.

Since the `_transferFees` function must iterate through the entirety of any `Fee` array, this can substantially increase the gas cost of processing the order in a way that would be unexpected for the buyer.

#### Suggested course of action

If possible, modify the `Order` struct to contain a sized `Fee` array that is set to a reasonable value based on business case. While this has a negative effect on gas in the common case, it mitigates this avenue for griefing.
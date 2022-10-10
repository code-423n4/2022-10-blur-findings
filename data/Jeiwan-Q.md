# [L-01] Unsafe ERC20 tokens transferring
## Targets
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125
## Impact
Seller might not receive the payment for their token in the case when the ERC20 transfer fails and doesn't revert.
## Proof of Concept
`ExecutionDelegate` is used to transfer payments from buyers to sellers. Payments are ERC20 tokens, and some ERC20
implementations (e.g. [ZRX](https://etherscan.io/address/0xe41d2489571d322189246dafa5ebde1f4699f498#code)) return a
boolean on failure instead of reverting. In case such token is used as payment token, a failed transfer won't cause a
revert. Since `ExecutionDelegate` [doesn't verify the return value of `safeTransfer`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125),
a failed transfer will result in a false positive: the seller won't get the payment, but the buyer will receive the token.

While only WETH (which reverts on failures) is allowed to be used as the payment token in the current implementation,
future modifications of the code might introduce new tokens, or the current version of the code might be deployed to
networks that use a non-reverting ERC20 wrapper implementation for their native currency.
## Recommended Mitigation Steps
Consider validating the return value of the `ERC20.safeTransferFrom` in `ExecutionDelegate`. Use OpenZeppelin's [SafeERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol)
as a reference.

# [NC-01] `Opened` and `Closed` events can be emitted when exchange is already opened/closed
## Targets
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43-L46
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47-L50
## Impact
`Opened` and `Closed` events can be emitted when is already in opened/closed state. This can cause confusion in off-chain
services that track activity of the exchange.
## Recommended Mitigation Steps
Disallow emitting of `Opened` and `Closed` events when exchange is already in the target state.

# [NC-02] Missing indexed field in events
## Targets
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L85
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L86
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L88
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L89
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L90
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L91
## Impact
Indexed fields allow to search events by values of indexed fields, which makes historical analysis of smart contracts
possible.
## Recommended Mitigation Steps
Add `indexed` fields to events that don't have them.

# [NC-03] `ecrecover` is malleable
## Targets
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L408
## Impact
The `ecrecover` function allows malleable signatures: a signature remain valid if its s-value gets flipped into the other
half of its range. Since a malleability check is missed in the code, it's allowed to submit signatures with either s-value.
However, this doesn't bear any risks, thus the non-critical status of the finding.
## Recommended Mitigation Steps
Consider adding [a malleability check](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L138-L149).

# [NC-04] Redundant v-value check in `_recover`
## Targets
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407
## Impact
The v-value check is redundant since all client implementations check it. See:
1. [Remove invalid signature check on v in ECDSA](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3457)
1. https://twitter.com/alexberegszaszi/status/1534461421454606336
1. [Explain that ecrecover only accepts v={27,28}](https://github.com/ethereum/yellowpaper/pull/860)
## Recommended Mitigation Steps
Consider removing the redundant v-value check.
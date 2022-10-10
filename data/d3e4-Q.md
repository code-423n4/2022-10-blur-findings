**[G-01] Code repetition in StandardPolicyERC721.sol and StandardPolicyERC1155.sol**
**[G-02] Redundant check**
**[G-03] ReentrancyGuarded.sol has a cheaper implementation**
**[G-04] Comparing to constant bool**
**[G-05] Declaring constants as `private` rather than `public` saves deployment gas**
**[G-06] Fixed bytes is cheaper than `string`**
**[G-07] Function not called by the contract itself can be `external` instead of `public`**
**[G-08] Use `calldata` instead of `memory` for function parameters that are read-only**
**[G-09] Unnecessary two-step assignment**
**[G-10] Emit a memory, rather than storage, variable when available**
**[G-11] Pre-compute `.length` before repeated usage, especially in loops**
**[G-12] Cache/precompute ` _whitelistedPolicies.length() - cursor`**
**[G-13] Pre-incrementing is cheaper than post-incrementing**
**[G-14] Uncheck integer calculations**
**[G-15] `require` is cheaper than `if`**
**[G-16] Custom errors are cheaper than require-statements**

&nbsp;

## [G-01] Code repetition in StandardPolicyERC721.sol and StandardPolicyERC1155.sol
StandardPolicyERC721.sol and StandardPolicyERC1155.sol are identical up to token names ("ERC721" and "ERC1155"). Consider combining them into one contract.
Also, within these contracts, the functions `canMatchMakerAsk`and `canMatchMakerBid` are identical up to the order of Bid/Ask in the variables `makerAsk`/`takerBid`. Consider combining these two functions into one, possibly into a private helper function if you want to retain the two function names `canMatchMakerAsk`and `canMatchMakerBid`.
I understand that they were written as two near copies in order to match IMatchingPolicy, but as BlurExchange deals specifically and only with ERC721 and ERC1155 I stand by this consideration to combine them.

&nbsp;

## [G-02] Redundant check
The check in [PolicyManager.sol#L26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26) is not necessary. `add` only returns `false` if `_whitelistedPolicies.contains(policy)`. If the error message is needed it can be triggered directly by the bool returned from `add`.
Similarly in [PolicyManager.sol#L37](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)

&nbsp;

## [G-03] ReentrancyGuarded.sol has a cheaper implementation
`reentrancyLock` can be private. It is also cheaper to implement it as a uint256 rather than a bool. For reference see OpenZeppelin's implementation: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol.

&nbsp;

## [G-04] Comparing to constant bool
Replace e.g. `foo == false` with `!foo`.

Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L267
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124

&nbsp;

## [G-05] Declaring constants as `private` rather than `public` saves deployment gas
The compiler will give `public` constants a getter method, which costs gas during deployment. As they are constants they can be read directly from the verified source code.

Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L58
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L59
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L23
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L26
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L29
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L33

&nbsp;

## [G-06] Fixed bytes is cheaper than `string`
As a rule of thumb, use bytes for arbitrary-length raw byte data and string for arbitrary-length string (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L58

&nbsp;

## [G-07] Function not called by the contract itself can be `external` instead of `public`

Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L102
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L21

&nbsp;

## [G-08] Use `calldata` instead of `memory` for function parameters that are read-only
Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L20
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L35
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L39

&nbsp;

## [G-09] Unnecessary two-step assignment
At [BlurExchange.sol#L389](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L389) `v`, `r` and `s` are set via temporary return values from `abi.decode()`. Save three assignments (9 gas) by replacing [L388-L389](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L388-L389) with this:
```
bytes32[] memory merklePath;
(merklePath, v, r, s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

&nbsp;

## [G-10] Emit a memory, rather than storage, variable when available
Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L221
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L230
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L239
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L247

&nbsp;

## [G-11] Pre-compute `.length` before repeated usage, especially in loops
Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L75-L77
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

&nbsp;

## [G-12] Cache/precompute ` _whitelistedPolicies.length() - cursor`
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L71-L72

&nbsp;

## [G-13] Pre-incrementing is cheaper than post-incrementing
Consider replacing e.g. `i++` with `++i`.

Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208 (also consider putting it directly in the emit)
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

&nbsp;

## [G-14] Uncheck integer calculations
Where integers cannot over-/underflow they may be `unchecked{}` to save gas. In loops, make the unchecked increment in the end of the loop.

Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L485
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

&nbsp;

## [G-15] `require` is cheaper than `if`
In `cancelOrder` an [if-clause](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L187) is used to check whether the order is already cancelled or filled. If it is nothing happens, and if it isn't a state change occurs and an event is emitted.
It is cheaper to use a `require`, which also has the benefit of directly informing the user that the order didn't change.
Consider replacing `if (!cancelledOrFilled[hash])` with `require(cancelledOrFilled[hash])`.

&nbsp;

## [G-16] Custom errors are cheaper than require-statements
See [this](https://blog.soliditylang.org/2021/04/21/custom-errors/). Consider replacing require-statements with a string message with a custom error in an if-revert.

Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L14


**Ownership issues**

BlurExchange, ExecutionDelegate and PolicyManager inherits from OpenZeppelin's Ownable or OwnableUpgradeable, which implement `renounceOwnership` and `transferOwnership`. A, perhaps accidental, misuse of these may leave the contracts without an owner, thus rendering many critical functions modified by `onlyOwner` inaccessible.
Consider reimplementing `renounceOwnership` to disable it, and reimplementing `transferOwnership` as a two-step process where the new owner is nominated for and has to accept the ownership.

&nbsp;

**weth issues**
[`_weth = weth`](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113) is missing an address(0) check and `weth` has no function to change it after initalization (which it should NOT have!), yet it is [declared](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L63) as a public variable. If it is incorrectly set the exchange will be useless, as sell orders become impossible. Consider making it a (private) constant.

&nbsp;

**Signature malleability**
[`_recover()`](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L401-L409) is susceptible to signature malleability, which allows replay attacks. Since the order hash of executed orders is stored this is currently not an issue, but if the contract logic is changed this vulnerabilty might materialize.
Consider checking that `s` is in the lower half range, i.e. `s <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0`, or using OpenZeppelin's [ECDSA.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/8d908fe2c20503b05f888dd9f702e3fa6fa65840/contracts/utils/cryptography/ECDSA.sol).

&nbsp;

**No check for invalid `ecrecover()` return value**
`ecrecover()` returns `0` on invalid input. Consider reverting `_recover()` if `ecrecover()` returns `0`.

&nbsp;

**StandardPolicyERC1155 doesn't allow for batch transfers**
A main feature of ERC1155, compared to ERC721, is batch transfers of tokens. But the amount to transfer is hardcoded as 1 in StandardPolicyERC1155.sol at [L33](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L33) and [L59](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L59).
Consider amending the policy to make use of the Order.amount for ERC1155.

&nbsp;

**Missing `address(0)` check**
Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L97
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L100

&nbsp;

**Explicitly mark visibility of state**
Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L33
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L37

&nbsp;

**Ambiguous parameter name `order`**
The parameter (`Input calldata order`](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L291) is a struct `Input` which in itself has an `Order` also named `order`, leading to ambiguity. Consider renaming the outer `Input calldata order` e.g. `input`.

&nbsp;

**Unused name of return variable**
Instances:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L115
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L127
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L142
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L29

&nbsp;

**Redundant/unused imports**
At [BlurExchange.sol#L5](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L5) Initializable.sol is imported, but not explicitly inherited in the contract. It is however imported and inherited in the two following imports of UUPSUpgradeable.sol and OwnableUpgradeable.sol.

ERC20.sol is imported at [BlurExchange.sol#L8](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L8) but never used.

&nbsp;

**Missing NatSpec**
Most of the code in scope is missing NatSpec.

&nbsp;

**Typos**
["musted" -> "must"](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L387)

&nbsp;

**Incorrect comments**
The function `_exists(address what)` does not ["Determine if the given address exists"](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L545) but whether `what` is a deployed contract.

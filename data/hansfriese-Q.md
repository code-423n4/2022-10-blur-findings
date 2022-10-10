## Low
### [L-01] Use of the vulnerable version of `OpenZeppelin`
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/package.json#L63

It uses a verion 4.4.1 of `OpenZeppelin` [here](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/package.json#L63) and versions lower than 4.7.3 are known to have a vulnerability as explained [here](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-4h98-2769-gh6h).

This protocol uses `ecrecover()` widely for several validations and we should use version 4.7.3 as there would be unexpected issues with the old version.

### [L-02] Use of Solidity version 0.8.13 which has known issues
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L2

The version 0.8.13 has some issues like [Vulnerability related to ABI-encoding](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/) and [Vulnerability related to 'Optimizer Bug Regarding Memory Side Effects of Inline Assembly'](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/).

The [EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L2) contains several encoding logics and there are some assemblies in the protocol also.

Recommend using the latest solidity version like 0.8.17.

### [L-03] Check address(0) before transfer funds
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L478
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L499

It doesn't validate `fees[i].recipient != address(0)` before transfer funds and there might be unexpected loss.

We should check `to != address(0)` in [_transferTo()](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L499).

### [L-04] Improper implementation of `ExecutionDelegate.transferERC20()`
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L119-L122
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L511

As we can see [here](https://github.com/d-xo/weird-erc20#missing-return-values), some tokens like `USDT` don't return a bool on `ERC20.transferFrom()` and `transferERC20()` will revert with such tokens.

Currently, this functions is used for `weth` but it should be fixed for global purpose.

Also, [some tokens doesn't revert on transfer failure](https://github.com/d-xo/weird-erc20#no-revert-on-failure) and the return value should be checked [here](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L511).

Recommend using `safeTransferFrom()` of Openzeppelin.


## Non-critical
### [N-01] Event is missing indexed fields
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L76

Each event should use three indexed fields if there are three or more fields

### [N-02] require()/revert() statements should have descriptive reason strings
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L183
- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452

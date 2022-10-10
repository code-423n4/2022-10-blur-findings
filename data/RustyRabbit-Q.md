# Low severity

## State variable set in constructor of implementation contract rather than initializer.

The initial value of the `reentrancyLock` state variable used for the reentrancy guard is set during construction of the implementation contract
[ReentrancyGuarded.sol#L10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)
This means the default value is not set for the proxy contract as the constructor of the implementation contract is never executed in the [proxy contract](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L21) (including setting default state variables outside the constructor).

Luckily the default value for a boolean is `false` which mitigates this issue. (and makes this a Low finding rather than Med/High)

It is however recommended to correct this by using an `initialize()` function in the implementation contract and call it during the initialization of the proxy contract. This limit the chance that if the implementation is altered in the future with other state variables the same mistake is made with possibly far greater impact.

# Non-critical

## `transferERC721Unsafe()` is never used

The ``transferERC721Unsafe()` function in  [ExecutionDelegate.sol] (https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L73) is never used. Consider removing it.


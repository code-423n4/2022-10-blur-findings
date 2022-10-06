# Gas Report
## [G-01] `++i` costs less gas than `i++`, especially when it's used in for-loops (`--i`/`i--` too)

Saves 6 gas per loop

_There are 5 instance(s) of this issue:_

[BlurExchange#cancelOrders](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)

```
File: contracts/BlurExchange.sol    #1

199:  for (uint8 i = 0; i < orders.length; i++) {
```

[BlurExchange#_transferFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

```
File: contracts/BlurExchange.sol    #2

476:  for (uint8 i = 0; i < fees.length; i++) {
```

[PolicyManager#viewWhitelistedPolicies](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)

```
File: contracts/PolicyManager.sol    #3

77:   for (uint256 i = 0; i < length; i++) {
```

[EIP712#_packFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

```
File: contracts/lib/EIP712.sol    #4

77:   for (uint256 i = 0; i < fees.length; i++) {
```

[MerkleVerifier#_computeRoot](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

```
File: contracts/lib/MerkleVerifier.sol    #5

38:   for (uint256 i = 0; i < proof.length; i++) {
```

## [G-02] `<array>.length` should not be looked up in every loop of a for-loop

The overheads outlined below are PER LOOP, excluding the first loop:

- storage arrays incur a Gwarmaccess (100 gas)
- memory arrays use `MLOAD` (3 gas)
- calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset

_There are 4 instance(s) of this issue:_

[BlurExchange#cancelOrders](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)

```
File: contracts/BlurExchange.sol    #1

199:  for (uint8 i = 0; i < orders.length; i++) {
```

[BlurExchange#_transferFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

```
File: contracts/BlurExchange.sol    #2

476:  for (uint8 i = 0; i < fees.length; i++) {
```
[EIP712#_packFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

```
File: contracts/lib/EIP712.sol    #3

77:   for (uint256 i = 0; i < fees.length; i++) {
```

[MerkleVerifier#_computeRoot](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

```
File: contracts/lib/MerkleVerifier.sol    #4

38:   for (uint256 i = 0; i < proof.length; i++) {
```










First Gas optimization - Using cache to contain "array.length"

-storage arrays incur a Gwarmaccess (100 gas)
-memory arrays use MLOAD (3 gas)
-calldata arrays use CALLDATALOAD (3 gas)

Caching the length of the array changes it  to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

Second Gas optimization - >= cost less gas than >

You can declare this return statement from this "return size > 0;" to this "return size >= 1;", both equals the same but >= cost less gas:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L557

Third Gas optimization - For-Loops: Index increments can be left unchecked

From Solidity v0.8 onwards, all arithmetic operations come with implicit overflow and underflow checks.
In for-loops, as it is impossible for the index to overflow, it can be left unchecked to save gas every iteration.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77
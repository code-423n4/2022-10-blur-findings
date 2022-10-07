| | issue |
| ----------- | ----------- |
| 1 | [Unused local variable](#1-unused-local-variable) |
| 2 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#2-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 3 | [can just use the argument instead of making a stack variable](#3-can-just-use-the-argument-instead-of-making-a-stack-variable) |
| 4 | [stack variable is only used once, so we can simply use the function in the `if` statement instead of making a stack variable](#4-stack-variable-is-only-used-once-so-we-can-simply-use-the-function-in-the-if-statement-instead-of-making-a-stack-variable) |
| 5 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#5-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 6 | [stack variable is only used once, just use the operation instead of making a stack variable](#6-stack-variable-is-only-used-once-just-use-the-operation-instead-of-making-a-stack-variable) |
| 7 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#7-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 8 | [not using the named return variables when a function returns, wastes deployment gas](#8-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 9 | [`++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)](#9-i-costs-less-gas-than-i-especially-when-its-used-in-for-loops---ii---too) |
| 10 | [`<array>.length` should not be looked up in every loop of a for-loop](#10-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop) |
| 11 | [can make the variable outside the loop to save gas](#11-can-make-the-variable-outside-the-loop-to-save-gas) |
| 12 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#12-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 13 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#13-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 14 | [require()/revert() strings longer than 32 bytes cost extra gas](#14-requirerevert-strings-longer-than-32-bytes-cost-extra-gas) |
| 15 | [use custom errors rather than revert()/require() strings to save deployment gas](#15-use-custom-errors-rather-than-revertrequire-strings-to-save-deployment-gas) |
| 16 | [use a more recent version of solidity](#16-use-a-more-recent-version-of-solidity) |
| 17 | [using `bool` for storage incurs overhead](#17-using-bool-for-storage-incurs-overhead) |
| 18 | [internal functions only called once can be inlined to save gas](#18-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 19 | [abi.encode() is less efficient than abi.encodepacked()](#19-abiencode-is-less-efficient-than-abiencodepacked) |
| 20 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#20-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 21 | [using private rather than public for constants, saves gas](#21-using-private-rather-than-public-for-constants-saves-gas) |
| 22 | [avoid an unnecessary sstore by not writing a default value for `bool`s](#22-avoid-an-unnecessary-sstore-by-not-writing-a-default-value-for-bools) |
| 23 | [bytes constants are more efficient than string constants](#23-bytes-constants-are-more-efficient-than-string-constants) |
| 24 | [public library function should be made private/internal](#24-public-library-function-should-be-made-privateinternal) |
| 25 | [public functions not called by the contract should be declared external instead](#25-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 26 | [use arguments instead of state var](#26-use-arguments-instead-of-state-var) |



## 1. Unused local variable
Some of the variables are fetched and allocated but not used

`merklePath` has not been used after being allocated
- [BlurExchange.sol#L388](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388)


## 2. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [EIP712.sol#L20](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20)
- [EIP712.sol#L23](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23)
- [EIP712.sol#L26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26)
- [EIP712.sol#L29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29)
- [EIP712.sol#L33](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L33)


## 3. can just use the argument instead of making a stack variable

use `size` instead of making `length` 

- [PolicyManager.sol#L69](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L69)


## 4. stack variable is only used once, so we can simply use the function in the `if` statement instead of making a stack variable

- [MerkleVerifier.sol#L22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L22)


## 5. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }

- [BlurExchange.sol#L485](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L485)


## 6. stack variable is only used once, just use the operation instead of making a stack variable

- [BlurExchange.sol#L485](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L485)


## 7. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

- [BlurExchange.sol#L208](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208)
- [BlurExchange.sol#L479](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479)


## 8. not using the named return variables when a function returns, wastes deployment gas

- [BlurExchange.sol#L419](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L419)

- [ERC1967Proxy.sol#L29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L29)


## 9. `++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)
Saves 6 gas per loop

- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

- [PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

- [EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

- [MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)


## 10. `<array>.length` should not be looked up in every loop of a for-loop
This reduce gas cost as show here https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5

- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

- [EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

- [MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)


## 11. can make the variable outside the loop to save gas

- [BlurExchange.sol#L477](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L477)

- [MerkleVerifier.sol#L39](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L39)


## 12. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol#L475](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475)
- [BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

- [PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

- [EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

- [MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)


## 13. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

- [PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

- [EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

- [MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)


## 14. require()/revert() strings longer than 32 bytes cost extra gas

- [BlurExchange.sol#L482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)

- [ExecutionDelegate.sol#L22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)


## 15. use custom errors rather than revert()/require() strings to save deployment gas

https://blog.soliditylang.org/2021/04/21/custom-errors/

all `require()`s need to change


## 16. use a more recent version of solidity

solidity 8.16 is available now.


## 17. using `bool` for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

- [BlurExchange.sol#L71](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L71)
- [BlurExchange.sol#L421](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L421)

- [ReentrancyGuarded.sol#L10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)

- [ExecutionDelegate.sol#L18](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L18)
- [ExecutionDelegate.sol#L19](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L19)


## 18. internal functions only called once can be inlined to save gas
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

- [BlurExchange.sol#L278](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L278)
- [BlurExchange.sol#L344](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L344)
- [BlurExchange.sol#L375](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L375)
- [BlurExchange.sol#L416](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L416)
- [BlurExchange.sol#L444](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L444)
- [BlurExchange.sol#L469](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L469)
- [BlurExchange.sol#L525](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L525)
- [BlurExchange.sol#L548](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L548)

- [EIP712.sol#L39](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L39)
- [EIP712.sol#L55](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L55)
- [EIP712.sol#L69](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L69)
- [EIP712.sol#L124](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L124)
- [EIP712.sol#L139](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L139)

- [ERC1967Proxy.sol#L29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L29)


## 19. abi.encode() is less efficient than abi.encodepacked()

- [EIP712.sol#L45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L45)
- [EIP712.sol#L61](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L61)
- [EIP712.sol#L91](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L91)
- [EIP712.sol#L107](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L107)
- [EIP712.sol#L132](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L132)
- [EIP712.sol#L147](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L147)


## 20. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol#L347](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L347)
- [BlurExchange.sol#L383](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L383)
- [BlurExchange.sol#L388](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388)
- [BlurExchange.sol#L403](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L403)
- [BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)


## 21. using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [BlurExchange.sol#L57](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57)
- [BlurExchange.sol#L58](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58)
- [BlurExchange.sol#L59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)

- [EIP712.sol#L20](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20)
- [EIP712.sol#L23](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23)
- [EIP712.sol#L26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26)
- [EIP712.sol#L29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29)


## 22. avoid an unnecessary sstore by not writing a default value for `bool`s

- [ReentrancyGuarded.sol#L10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)


## 23. bytes constants are more efficient than string constants
If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

- [BlurExchange.sol#L57](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57)
- [BlurExchange.sol#L58](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58)

- [EIP712.sol#L13](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L13)
- [EIP712.sol#L14](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L14)


## 24. public library function should be made private/internal

Changing from public will remove the compiler-introduced checks for msg.value and decrease the contract’s method ID table size

- [MerkleVerifier.sol#L17](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17)


## 25. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

- [MerkleVerifier.sol#L17](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17)


## 26. use arguments instead of state var

we can use the arguments in emits of setters instead of state var to save gas

- [BlurExchange.sol#L221](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L221)
- [BlurExchange.sol#L230](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L230)
- [BlurExchange.sol#L239](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L239)
- [BlurExchange.sol#L247](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L247)

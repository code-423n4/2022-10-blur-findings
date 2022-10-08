## `++i` costs less gas than `i++`, especially when it’s used in `for`-loops (`--i`/`i--` too)
Saves 5 gas per loop

Instances include: 
[`contracts/PolicyManager.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
[`contracts/lib/EIP712.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
[`contracts/BlurExchange.sol:199`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[`contracts/BlurExchange.sol:476`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
[`contracts/lib/MerkleVerifier.sol:38`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

## Cache array length in for loops can save gas
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.  
Caching the array length in the stack saves around 3 gas per iteration.  

Instances include: 
[`contracts/lib/EIP712.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
[`contracts/BlurExchange.sol:199`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[`contracts/BlurExchange.sol:476`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
[`contracts/lib/MerkleVerifier.sol:38`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

## Not initializing `unit` variable to default value of zero
It costs more gas. 

Instances include: 
[`contracts/PolicyManager.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
[`contracts/lib/EIP712.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
[`contracts/BlurExchange.sol:199`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[`contracts/BlurExchange.sol:475`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475)
[`contracts/BlurExchange.sol:476`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
[`contracts/lib/MerkleVerifier.sol:38`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

## Require/Revert strings longer than 32 bytes cost additional gas
[`contracts/ExecutionDelegate.sol:22`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)
[`contracts/BlurExchange.sol:482`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)

## Use custom errors rather than REVERT()/REQUIRE() to save gas
Instances include:
Custom errors from solidity 0.8.4 are cheaper than revert strings, custom errors are defined using the `error` statement can use inside and outside the contract.
source [https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/)
I suggest replacing revert error strings with custom error

Instances include: 
[`contracts/lib/ReentrancyGuarded.sol:14`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14)
[`contracts/PolicyManager.sol:26`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26)
[`contracts/PolicyManager.sol:37`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37)
[`contracts/ExecutionDelegate.sol:22`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)
[`contracts/ExecutionDelegate.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77)
[`contracts/ExecutionDelegate.sol:92`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92)
[`contracts/ExecutionDelegate.sol:108`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108)
[`contracts/ExecutionDelegate.sol:124`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124)
[`contracts/BlurExchange.sol:36`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36)
[`contracts/BlurExchange.sol:139`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139)
[`contracts/BlurExchange.sol:140`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L140)
[`contracts/BlurExchange.sol:142`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L142)
[`contracts/BlurExchange.sol:143`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L143)
[`contracts/BlurExchange.sol:219`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219)
[`contracts/BlurExchange.sol:228`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228)
[`contracts/BlurExchange.sol:237`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237)
[`contracts/BlurExchange.sol:318`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318)
[`contracts/BlurExchange.sol:407`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407)
[`contracts/BlurExchange.sol:424`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424)
[`contracts/BlurExchange.sol:428`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L428)
[`contracts/BlurExchange.sol:431`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L431)
[`contracts/BlurExchange.sol:482`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)
[`contracts/BlurExchange.sol:534`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534)

## Calls to `keccak256` should use `immutable` instead of constants

Instances include: 
[`contracts/lib/EIP712.sol:20`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20)
[`contracts/lib/EIP712.sol:23`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23)
[`contracts/lib/EIP712.sol:26`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26)
[`contracts/lib/EIP712.sol:29`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29)
[`contracts/lib/EIP712.sol:33`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L33)

## BYTES CONSTANTS ARE CHEAPER THAN STRING CONSTANTS
If the string can fit into 32 bytes, then `bytes32` is cheaper than `string`. `string` is a dynamically sized-type, which has current limitations in Solidity compared to a statically sized variable. This means extra gas spent upon deployment and every time the constant is read.

Instances include: 
[`contracts/BlurExchange.sol:57`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57)
[`contracts/BlurExchange.sol:58`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58)

## `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow
[`contracts/PolicyManager.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
[`contracts/lib/EIP712.sol:77`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
[`contracts/BlurExchange.sol:199`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[`contracts/BlurExchange.sol:476`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
[`contracts/lib/MerkleVerifier.sol:38`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

## Use `calldata` instead of `memory` for function parameters  
  
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. Only use memory if the variable needs to be modified.  
  
Instances include:
[`contracts/lib/MerkleVerifier.sol:35`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L35)

## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Instances include:
[`contracts/BlurExchange.sol:476`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

## `abi.encode()` is less efficient than `abi.encodepacked()`
Instances include:
[`contracts/lib/EIP712.sol:45`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L45)
[`contracts/lib/EIP712.sol:61`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L61)
[`contracts/lib/EIP712.sol:91`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L91)
[`contracts/lib/EIP712.sol:107`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L107)
[`contracts/lib/EIP712.sol:132`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L132)
[`contracts/lib/EIP712.sol:147`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L147)

## Use assembly to check for address(0)
Saves 6 gas per instance if using assembly to check for address(0)
e.g.

```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

Instances include:
[`contracts/BlurExchange.sol:219`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219)
[`contracts/BlurExchange.sol:228`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228)
[`contracts/BlurExchange.sol:237`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237)
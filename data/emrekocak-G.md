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
It cost more gas. 

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
Custom error from solidity 0.8.4 are cheaper than revert strings, custom error are defined using the `error` statement can use inside and outside the contract.
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
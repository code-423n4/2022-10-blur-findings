# Gas Optimizations Report for Blur Exchange contest

## Overview
During the audit, 7 gas issues were found.

№ | Title | Instance Count
--- | --- | --- 
G-1 | [Postfix increment](#g-1-postfix-increment) | 5
G-2 | [<>.length in loops](#g-2-length-in-loops) | 4
G-3 | [Initializing variables with default value](#g-3-initializing-variables-with-default-value) | 7
G-4 | [Some variables can be immutable](#g-4-some-variables-can-be-immutable) | 1
G-5 | [x += y is more expensive than x = x + y](#g-5-x--y-is--more-expensive-than-x--x--y) | 2
G-6 | [Using unchecked blocks saves gas](#g-6-using-unchecked-blocks-saves-gas) | 5
G-7 | [Custom errors may be used](#g-7-custom-errors-may-be-used) | 20

## Gas Optimizations Findings (7)
### G-1. Postfix increment
##### Description
Prefix increment costs less gas than postfix.

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

##### Recommendation
Consider using prefix increment  where it is relevant. 

#
### G-2. <>.length in loops
##### Description
Reading the length of an array at each iteration of the loop consumes extra gas.

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

##### Recommendation
Store the length of an array in a variable before the loop, and use it.

#
### G-3. Initializing variables with default value
##### Description
It costs gas to initialize integer variables with 0 or bool variables with false but it is not necessary.

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

##### Recommendation
Remove initialization for default values.  
For example:
``` for (uint256 i; i < array.length; ++i) {```

#
### G-4. Some variables can be immutable
##### Description
Using immutables is cheaper than storage-writing operations.

##### Instances
- [```address public weth;```](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63)

##### Recommendation
Use immutables where possible.  
Change to ```address public immutable weth;```

#
### G-5. ```x += y``` is  more expensive than ```x = x + y```
##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479

##### Recommendation
Use ```x = x + y``` instead of ```x += y```.
Use ```x = x - y``` instead of ```x -= y```.

#
### G-6. Using unchecked blocks saves gas
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible, some gas can be saved by using unchecked blocks.

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

#
### G-7. Custom errors may be used
##### Description
[Custom errors from Solidity 0.8.4 are cheaper than revert strings.](https://blog.soliditylang.org/2021/04/21/custom-errors/)

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L140
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L142
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L143
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L428
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L431
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534

##### Recommendation
For example, change:
```
require(msg.sender == owner, "INVALID_ADDRESS");
```
to:
```
error InvalidAddress();
...
if (msg.sender != owner) revert InvalidAddress();
```
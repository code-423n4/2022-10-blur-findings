
|   | Title  | Instances  |
|---|---|:---:|
| 1  | Using values `1` and `2` instead of `false` and `true` for boolean-like variables saves gas  | 2 | 
| 2  | Using `bool` instead of `uint256` costs extra gas overhead  | 2  | 
| 3  | Non-state variables can be made immutable  | 1  | 
| 4  | `++i` instead of `i++` saves gas  | 5  | 
| 5  | `++var` instead of `var += 1` saves gas  | 1  | 
| 6  | Caching the array length saves gas per iteration  | 5  | 
| 7  | Increments can be `unchecked` | 6  | 
| 8  | Fixed size `bytes` constants are more efficient than `string` constants  | 2  | 
| 9  | `require` with reason strings more than 32 bytes costs more gas  | 2  | 

## [G-01] Using values `1` and `2` instead of `false` and `true` for boolean-like variables saves gas

2 instances

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L33

Setting a state variable from zero to non-zero costs a lot more gas than non-zero to non-zero. Costs more gas upon deployment, but saves significant gas for every reentrancy-guarded operations.

## [G-02] Using `bool` instead of `uint256` costs extra gas overhead

2 instances

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L71
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10

## [G-03] Non-state variables can be made immutable

1 instance

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63

Since `weth` is an immutable, it is possible to create a constructor and initialize the variable there. 

It is possible in an upgradeable context because immutables are embedded in the contract's bytecode, not its storage.

## [G-04] `++i` instead of `i++` saves gas

5 instances

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

## [G-05] `++var` instead of `var += 1` saves gas

1 instance

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208

## [G-06] Caching the array length saves gas per iteration

Applies to all instances of **[G-04]**

It saves a small amount of gas for caching the array length, instead of reading them per loop, even when the array is memory or calldata (3 gas per loop in such case).

## [G-07] Increments can be `unchecked`

Applies to all instances of **[G-04]** and **[G-05]**. Saves about 30-40 gas per operation.

## [G-08] Fixed size `bytes` constants are more efficient than `string` constants

2 instances

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58

Replace `string constant` with `bytes(1..32) constant`. In general go for fixed-size data types when possible.

## [G-09] `require` with reason strings more than 32 bytes costs more gas

2 instances

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

Can be changed to `"Total fees exceed price"`

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22

Can be changed to `"Contract is not approved"`

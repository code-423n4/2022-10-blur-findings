# **Gas optimizations**


## [G-01] Usage of boolean state variable in storage

Use `uint256(1)` and `uint256(2)` for true/false to avoid a `Gwarmaccess` (100 gas) for the extra `SLOAD`, and to avoid `Gsset` (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.
See more [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)


- ReentrancyGuarded.sol
  - [ReentrancyGuarded.sol#10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)




## [G-02] Avoid emitting a storage variable when a memory vairable is available
When the argument of the function and the memory value are the same, you should rather emit the argument variable (memory value).

- BlurExchange.sol
  - [BlurExchange.sol#221](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L221) i.e. replace `executionDelegate` by `_executionDelegate`
  - [BlurExchange.sol#230](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L230) i.e. replace `policyManager` by `_policyManager`
  - [BlurExchange.sol#239](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L239) i.e. replace `oracle` by `_oracle`
  - [BlurExchange.sol#247](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L247) i.e. replace `blockRange` by `_blockRange`



## [G-03] Set to `payable` permissionened functions that are guaranteed to revert for normal users
It will skip test that will lower the deployment and usage costs.
The extra opcodes avoided are `CALLVALUE(2)`, `DUP1(3)`, `ISZERO(3)`, `PUSH2(3)`, `JUMPI(10)`, `PUSH1(3)`, `DUP1(3)`, `REVERT(0)`, `JUMPDEST(1)`, `POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

1. BlurExchange.sol
  - [BlurExchange.sol#43](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43)
  - [BlurExchange.sol#47](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47)
  - [BlurExchange.sol#53](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53)
  - [BlurExchange.sol#217](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L217)
  - [BlurExchange.sol#226](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L226)
  - [BlurExchange.sol#235](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L235)
  - [BlurExchange.sol#244](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L244)



2. ExecutionDelegate.sol
  - [ExecutionDelegate.sol#36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36)
  - [ExecutionDelegate.sol#45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45)


3. PolicyManager.sol
  - [PolicyManager.sol#25](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L25)
  - [PolicyManager.sol#36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36)


## [G-04] Use `!= 0` instead of `> 0` on uint variables
uint variables will never be lower than 0. Therefore, > 0 and != 0 have same meanings. Using != 0 can reduce the gas deployment cost, so it is worth using != 0 wherever possible.

1. BlurExchange.sol
  - [BlurExchange.sol#557](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557)



## [G-05] Use prefix not postfix especially in loops
Saves 6 gas per loop.

1. BlurExchange.sol
  - [BlurExchange.sol#199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
  - [BlurExchange.sol#476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)


2. PolicyManager.sol
  - [PolicyManager.sol#77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)


3. EIP712.sol
  - [EIP712.sol#77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)


4. MerkleVerifier.sol
  - [MerkleVerifier.sol#38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)


## [G-06] Do not initialize non constant/non immutable vairable to zero
Not overwriting the default value for stack variable saves 8 gas per loop.

1. BlurExchange.sol
  - [BlurExchange.sol#199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
  - [BlurExchange.sol#476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)


2. PolicyManager.sol
  - [PolicyManager.sol#77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)


3. EIP712.sol
  - [EIP712.sol#77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)



4. MerkleVerifier.sol
  - [MerkleVerifier.sol#38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)


Recommended mitigation steps: \
Do not initialize them to 0 since it is the default value.


## [G-07] `++i/i++` should be unchecked{++i}/unchecked{i++} when it is not possible to overflow
In solidity 0.8.0 and above, checked arithmetic is implemented but can be deactivated by using `unchecked` keyword. This saves 30-40gas per loop. Since `i` is bounded in the loop, there is no need for checked arithmetic if `orders.length`, `fee.length` < 2<sup>8</sup>-1 and if `orders.length`, `length`, `proof.length` < 2<sup>256</sup>-1

1. BlurExchange.sol
  - [BlurExchange.sol#199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
  - [BlurExchange.sol#476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)


2. PolicyManager.sol
  - [PolicyManager.sol#77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)


3. EIP712.sol
  - [EIP712.sol#77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)


4. MerkleVerifier.sol
  - [MerkleVerifier.sol#38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

## [G-8]`variable.length` should not be looked up in every loop of a for-loop
The overheads outlined below are PER LOOP, excluding the first loop

  - storage arrays incur a Gwarmaccess (100 gas)
  - memory arrays use `MLOAD` (3 gas)
  - calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset

1. BlurExchange.sol
  - [BlurExchange.sol#199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
  - [BlurExchange.sol#476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)


2. EIP712.sol
  - [EIP712.sol#77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)


3. MerkleVerifier.sol
  - [MerkleVerifier.sol#38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)


## [G-09] require()/revert() strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 incurs an `MSTORE` which cost 3 gas.

1. BlurExchange.sol
  - [BlurExchange.sol#482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)


2. ExecutionDelegate.sol
  - [ExecutionDelegate.sol#22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)

# Quality Assurance Report

## Issues 


|   |      Issue     |  Instances |
|----------|-------------|:------:|
| 1 | Missing checks for address(0x0) when assigning values to address state variables | 2 |
| 2  | abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccack256()    |   4 |


### 1. Missing checks for address(0x0) when assigning values to address state variables
Zero address should be checked for state variables and some parameters in functions like mints, withdrawals... A zero address can lead into problems.

### POC 

```solidity
    weth = _weth;
    oracle = _oracle;
```
### Instances
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L116
```

### Mitigation 
Check zero address for state variables before assigning to them a value





### 2. abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccack256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456) , but abi.encode(0x123,0x456) => 0x0...1230...456 ).

### POC 

```solidity
return keccak256(abi.encodePacked(feeHashes));
L 117 -         return keccak256(abi.encodePacked( 
```
### Instances
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L80
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L117
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L129
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L144
```
Mitigation: “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.




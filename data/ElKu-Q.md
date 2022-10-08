 # Low Risk Issues

## Summary Of Findings:

|  | Issue 
-- | -- 
1 | Add a timelock to critical functions
2 | Missing zero-address checks in the `initialize` function in `BlurExchange.sol`
3 | Check for `paymentToken` validity at the beginning of the function

## Details on Findings:

### 1. <ins>Add a timelock to critical functions</ins>

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

Instances of this are:
 1. [setExecutionDelegate](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L215) function
 2. [setPolicyManager](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L224) function
 3. [setOracle](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L233) function
 4. [setBlockRange](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L242) function

### 2. <ins>Missing zero-address checks in the `initialize` function in `BlurExchange.sol`</ins>
The [initialize](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113-L116) function sets important contract addresses in the `BlurExchange` contract. But these contract addresses are set without a zero-address check. To be on the safer side, I recommend a zero-address check here. Note that zero-address checks are performed on the [set functions](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L215-L240) of these addresses.

### 3. <ins>Check for `paymentToken` validity at the beginning of the function</ins>
The [_transferTo](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L513) internal function reverts when the paymentToken is neither `address(0)` nor `weth`. This means the `execute` function will also revert as the mentioned [internal function is called](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L147-L153) inside the `execute` function.

As the value of `paymentToken` is dependent on the input arguement, it makes sense that the value of `paymentToken` is checked at the earliest before we do any further calculations. This increases readability and will even save gas upon a revert. 

This can be mitigated with a simple `require` statement in the execute function:
```solidity
require(sell.order.paymentToken == address(0) || sell.order.paymentToken == weth, "Wrong paymentToken");
```


 # Non-Critical Issues

## Summary Of Findings:

|  | Issue 
-- | -- 
1 | Use non-assembly method when available
2 | Missing Natspec comments
3 | `require()`/`revert()` statements should have descriptive reason strings
4 | Limit the range of BlockRange
5 | Mention explicitly `uint256` instead of `uint`
6 | Misleading naming of function arguments
7 | Using `==` to check the value of a `bool` variable

## Details on Findings:

### 1. <ins>Use non-assembly method when available</ins>

There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it’s best to avoid using it where it’s not necessary.

One instance found in [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L554-L556)

**Mitigation:-**
```solidity
// the below block
        assembly {
            size := extcodesize(what)
        }
// could be written in regular solidity as:
		size = what.code.length;
```

### 2. <ins>Missing Natspec comments</ins>
The following functions are missing natspec comments:
 1. [setExecutionDelegate](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L215) function
 2. [setPolicyManager](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L224) function
 3. [setOracle](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L233) function
 4. [setBlockRange](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L242) function


### 3. <ins>require()/revert() statements should have descriptive reason strings</ins>
There are 3 instances of this issue in one file:

File - [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol)
```solidity
Line 134:	require(sell.order.side == Side.Sell);
Line 183:	require(msg.sender == order.trader);
Line 452:	require(msg.value == price);
```

### 4. <ins>Limit the range of BlockRange</ins>
Limiting the range of important admin variables is a recommended feature on most protocols. As this gives users an idea on how big the BlockRange can be in the worst/best case.

This can be easily done by a `require` statement in function [setBlockRange](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L242)
```solidity
require(_blockRange >= MIN_BLOCKRANGE && _blockRange <= MAX_BLOCKRANGE, "INVALID BLOCKRANGE");
```

### 5. <ins>Mention explicitly `uint256` instead of `uint`</ins></ins>
Though using `uint` is not wrong, for the sake of readability its good to mention explicitly `uint256` when declaring variables.

There are 3 instances of this issue in one file:

File - [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol)
```solidity
Line 096:	uint chainId,
Line 101:	uint _blockRange,
Line 553:	uint size;
```
### 6. <ins>Misleading naming of function arguments</ins>
The [execute](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L128) function has two arguments `sell` and `buy` of type `Input`. These inputs are passed into the [_validateSignatures](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L291) internal function later on. And there, its named as `order`. This can be misleading and hurts readability as the `Input` structure already contains a sub-structure called `order` of type `Order` and is used within the main calling function `execute`. 

I suggest renaming the input arguement of `_validateSignatures` function to something different. For example:
```solidity
function _validateSignatures(Input calldata executeInput, bytes32 orderHash)
```

### 7. <ins>Using `==` to check the value of a `bool` variable</ins>
When you want to check the value of a bool variable is `true` or `false`, there is no need to use the `==` operator. 

For example:
```solidity
bool boolVar;

if(boolVar == true) {}
// is same as:
if(boolVar)
//AND
if(boolVar == false) {}
// is same as:
if(!boolVar)
```
The above coding style increases the readability and professionality of the code.

There are 5 instances of this issue in two files:

File - [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol)
```solidity
Line 267:	cancelledOrFilled[orderHash] == false
```
File - [ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol)
```solidity
Line 077:	require(revokedApproval[from] == false, "User has revoked approval");
Line 092:	require(revokedApproval[from] == false, "User has revoked approval");
Line 108:	require(revokedApproval[from] == false, "User has revoked approval");
Line 124:	require(revokedApproval[from] == false, "User has revoked approval");
```

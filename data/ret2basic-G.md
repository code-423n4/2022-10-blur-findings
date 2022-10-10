# Blur Exchange Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (1 instance)
2. Cache `<array>.length` (4 instances)
3. Use `unchecked{}` to suppress overflow/underflow check (5 instances)
4. Long `require()`/`revert()` string (2 instances)
5. Using `bool`s for storage incurs overhead (2 instances)
6. Use `!= 0` instead of `> 0` when comparing uint (1 instance)
7. Empty blocks should be removed or emit something (1 instance)
8. Don't initialize variables with default value (7 instances)
9. Use `++i`/`--i` instead of `i++`/`i--` (5 instances)
10. Use `abi.encodePacked()` instead of `abi.encode()` (6 instances)
11. Use `private` instead of `public` for constants (3 instances)
12. Use custom errors instead of `revert()`/`require()` strings (24 instances)

Total 61 instances of 12 issues.

## 1. Use `calldata` instead of `memory` (1 instance)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
contracts/lib/EIP712.sol::39 => function _hashDomain(EIP712Domain memory eip712Domain)
```

## 2. Cache `<array>.length` (4 instances)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {

contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## 3. Use `unchecked{}` to suppress overflow/underflow check (5 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {

contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {

contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## 4. Long `require()`/`revert()` string (2 instances)

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

```solidity
contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");

contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
```

## 5. Using `bool`s for storage incurs overhead (2 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;

contracts/BlurExchange.sol::421 => bool canMatch;
```

## 6. Use `!= 0` instead of `> 0` when comparing uint (1 instance)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
contracts/BlurExchange.sol::557 => return size > 0;
```

## 7. Empty blocks should be removed or emit something (1 instance)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
contracts/BlurExchange.sol::53 => function _authorizeUpgrade(address) internal override onlyOwner {}
```

## 8. Don't initialize variables with default value (7 instances)

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;

contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {

contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {

contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol::475 => uint256 totalFee = 0;

contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## 9. Use `++i`/`--i` instead of `i++`/`i--` (5 instances)

Using `++i`/`--i` saves 6 gas per loop.

```solidity
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {

contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {

contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## 10. Use `abi.encodePacked()` instead of `abi.encode()` (6 instances)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
contracts/lib/EIP712.sol::45 => abi.encode(

contracts/lib/EIP712.sol::61 => abi.encode(

contracts/lib/EIP712.sol::91 => abi.encode(

contracts/lib/EIP712.sol::107 => abi.encode(nonce)

contracts/lib/EIP712.sol::132 => keccak256(abi.encode(

contracts/lib/EIP712.sol::147 => keccak256(abi.encode(
```

## 11. Use `private` instead of `public` for constants (3 instances)

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
contracts/BlurExchange.sol::57 => string public constant name = "Blur Exchange";

contracts/BlurExchange.sol::58 => string public constant version = "1.0";

contracts/BlurExchange.sol::59 => uint256 public constant INVERSE_BASIS_POINT = 10000;
```

## 12. Use custom errors instead of `revert()`/`require()` strings (24 instances)

Using `require()`/`revert()` strings is expensive. Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
contracts/lib/ReentrancyGuarded.sol::14 => require(!reentrancyLock, "Reentrancy detected");

contracts/PolicyManager.sol::26 => require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

contracts/PolicyManager.sol::37 => require(_whitelistedPolicies.contains(policy), "Not whitelisted");

contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");

contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");

contracts/ExecutionDelegate.sol::92 => require(revokedApproval[from] == false, "User has revoked approval");

contracts/ExecutionDelegate.sol::108 => require(revokedApproval[from] == false, "User has revoked approval");

contracts/ExecutionDelegate.sol::124 => require(revokedApproval[from] == false, "User has revoked approval");

contracts/BlurExchange.sol::36 => require(isOpen == 1, "Closed");

contracts/BlurExchange.sol::139 => require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

contracts/BlurExchange.sol::140 => require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

contracts/BlurExchange.sol::142 => require(_validateSignatures(sell, sellHash), "Sell failed authorization");

contracts/BlurExchange.sol::143 => require(_validateSignatures(buy, buyHash), "Buy failed authorization");

contracts/BlurExchange.sol::219 => require(address(_executionDelegate) != address(0), "Address cannot be zero");

contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");

contracts/BlurExchange.sol::237 => require(_oracle != address(0), "Address cannot be zero");

contracts/BlurExchange.sol::318 => require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

contracts/BlurExchange.sol::407 => require(v == 27 || v == 28, "Invalid v parameter");

contracts/BlurExchange.sol::424 => require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

contracts/BlurExchange.sol::428 => require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

contracts/BlurExchange.sol::431 => require(canMatch, "Orders cannot be matched");

contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");

contracts/BlurExchange.sol::513 => revert("Invalid payment token");

contracts/BlurExchange.sol::534 => require(_exists(collection), "Collection does not exist");
```

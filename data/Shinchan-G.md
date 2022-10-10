# **Gas Optimization**

Serial No. | Issue Name | Instances
--- | --- | ---
G-1 | Pre increment costs less gas as compared to Post increment | 6
G-2 | Increments can be unchecked | 5
G-3 | array.length should not be looked up in every loop of a for-loop | 4
G-4 | Use custom errors rather than revert()/require() strings to save deployment gas | 23
G-5 | Usage of assert() instead of require() | 1
G-6 | Boolean Comparision | 4
G-7 | Strict inequalities (>) are more expensive than non-strict ones (>=) | 2
G-8 | x += y costs more gas than x = x + y for state variables | 2
G-9 | abi.encode() is less efficient than abi.encodePacked() | 6
G-10 | Variables: No need to explicitly initialize variables with default values | 2
G-11 | Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | 5
G-12 | Reduce the size of error messages (Long revert Strings) | 3
G-13 | Using bools for storage incurs overhead | 5
G-14 | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | 5
G-15 | Use Assembly to check for address(0) | 4

----
***Total instances found in this contest: 77 | Among all files in scope***


## 1. Pre-increment costs less gas as compared to Post-increment :

++i costs less gas as compared to i++ for unsigned integer, as per-increment is cheaper(its about 5 gas per iteration cheaper)

i++ increments i and returns initial value of i. Which means

```
uint i =  1;
i++; // ==1 but i ==2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // ==2 and i ==2 , no need for temporary variable here
```

In the first case, the compiler has create a temporary variable (when used) for returning 1 instead of 2.

### Instances:
[PolicyManager.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
```
contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
```
[EIP712.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
```
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
```
[MerkleVerifier.sol:L38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
```
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
```
[BlurExchange.sol:L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
```
contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
```
[BlurExchange.sol:L476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
```
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```
[BlurExchange.sol:L208](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208)
```
contracts/BlurExchange.sol:208:        nonces[msg.sender] += 1;
```

### Recommendations:
Change post-increment to pre-increment.

-----

## 2. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

### Instances:
[PolicyManager.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
```
contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
```
[EIP712.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
```
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
```
[MerkleVerifier.sol:L38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
```
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
```
[BlurExchange.sol:L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
```
contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
```
[BlurExchange.sol:L476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
```
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```
### Reference:

[https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops](https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops)

-----


## 3. An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

### Instances:
[EIP712.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
```
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
```
[MerkleVerifier.sol:L38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
```
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
```
[BlurExchange.sol:L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
```
contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
```
[BlurExchange.sol:L476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
```
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```


### Remediation:
Here, I suggest storing the array's length in a variable before the for-loop, and use it instead.

-----

## 4. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Instances

[PolicyManager.sol:L26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26)
```
contracts/PolicyManager.sol:26:        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
```
[PolicyManager.sol:L37](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)
```
contracts/PolicyManager.sol:37:        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```
[ReentrancyGuarded.sol:L14](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L14)
```
contracts/lib/ReentrancyGuarded.sol:14:        require(!reentrancyLock, "Reentrancy detected");
```
[BlurExchange.sol:L36](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L36)
```
contracts/BlurExchange.sol:36:        require(isOpen == 1, "Closed");
```
[BlurExchange.sol:L139](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139)
```
contracts/BlurExchange.sol:139:        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
```
[BlurExchange.sol:L140](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L140)
```
contracts/BlurExchange.sol:140:        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
```
[BlurExchange.sol:L142](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L142)
```
contracts/BlurExchange.sol:142:        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
```
[BlurExchange.sol:L143](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L143)
```
contracts/BlurExchange.sol:143:        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
```
[BlurExchange.sol:L219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219)
```
contracts/BlurExchange.sol:219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
```
[BlurExchange.sol:L228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228)
```
contracts/BlurExchange.sol:228:        require(address(_policyManager) != address(0), "Address cannot be zero");
```
[BlurExchange.sol:L237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)
```
contracts/BlurExchange.sol:237:        require(_oracle != address(0), "Address cannot be zero");
```
[BlurExchange.sol:L318](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
```
contracts/BlurExchange.sol:318:            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
```
[BlurExchange.sol:L407](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407)
```
contracts/BlurExchange.sol:407:        require(v == 27 || v == 28, "Invalid v parameter");
```
[BlurExchange.sol:L424](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424)
```
contracts/BlurExchange.sol:424:            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
```
[BlurExchange.sol:L428](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428)
```
contracts/BlurExchange.sol:428:            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
```
[BlurExchange.sol:L431](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L431)
```
contracts/BlurExchange.sol:431:        require(canMatch, "Orders cannot be matched");
```
[BlurExchange.sol:L482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)
```
contracts/BlurExchange.sol:482:        require(totalFee <= price, "Total amount of fees are more than the price");
```
[BlurExchange.sol:L534](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534)
```
contracts/BlurExchange.sol:534:        require(_exists(collection), "Collection does not exist");
```
[ExecutionDelegate.sol:L22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
```
contracts/ExecutionDelegate.sol:22:        require(contracts[msg.sender], "Contract is not approved to make transfers");
```
[ExecutionDelegate.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
```
contracts/ExecutionDelegate.sol:77:        require(revokedApproval[from] == false, "User has revoked approval");
```
[ExecutionDelegate.sol:L92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
```
contracts/ExecutionDelegate.sol:92:        require(revokedApproval[from] == false, "User has revoked approval");
```
[ExecutionDelegate.sol:L108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
```
contracts/ExecutionDelegate.sol:108:        require(revokedApproval[from] == false, "User has revoked approval");
```
[ExecutionDelegate.sol:L124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)
```
contracts/ExecutionDelegate.sol:124:        require(revokedApproval[from] == false, "User has revoked approval");
```


### Remediation:
I suggest replacing revert strings with custom errors.

-----

## 5. Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded the remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see [https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions](https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions)).
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require unless a Panic really is intended.

### Instances:
[ERC1967Proxy.sol:L22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L22)
```
contracts/lib/ERC1967Proxy.sol:22:        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```

### References:

[https://code4rena.com/reports/2022-05-bunker/#g-08-usage-of-assert-instead-of-require](https://code4rena.com/reports/2022-05-bunker/#g-08-usage-of-assert-instead-of-require)

-----


## 6. Boolean Comparision

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here

### Instances:
[ExecutionDelegate.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
```
contracts/ExecutionDelegate.sol:77:        require(revokedApproval[from] == false, "User has revoked approval");
```
[ExecutionDelegate.sol:L92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
```
contracts/ExecutionDelegate.sol:92:        require(revokedApproval[from] == false, "User has revoked approval");
```
[ExecutionDelegate.sol:L108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
```
contracts/ExecutionDelegate.sol:108:        require(revokedApproval[from] == false, "User has revoked approval");
```
[ExecutionDelegate.sol:L124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)
```
contracts/ExecutionDelegate.sol:124:        require(revokedApproval[from] == false, "User has revoked approval");
```
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147](https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147)


-----

## 7. Strict inequalities (>) are more expensive than non-strict ones (>=)

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas. I suggest using >= instead of > to avoid some opcodes here:

### Instances:
[BlurExchange.sol:L169](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L169)
```
contracts/BlurExchange.sol:169:            sell.order.listingTime > buy.order.listingTime ? sell.order.trader : buy.order.trader,
```
[BlurExchange.sol:L557](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L557)
```
contracts/BlurExchange.sol:557:        return size > 0;
```
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than](https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than)


-----

## 8. x += y costs more gas than x = x + y for state variables

### Instances:
[BlurExchange.sol:L208](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208)
```
contracts/BlurExchange.sol:208:        nonces[msg.sender] += 1;
```
[BlurExchange.sol:L479](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L479)
```
contracts/BlurExchange.sol:479:            totalFee += fee;
```
### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


-----
## 9. abi.encode() is less efficient than abi.encodepacked()

Use abi.encodepacked() instead of abi.encode()

### Instances:
** PLEASE ADD LINK TO FILES INSTEAD OF CODE FOR THIS ONE.. **
[EIP712.sol:L45](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L45)
```
contracts/lib/EIP712.sol:45:            abi.encode(
```
[EIP712.sol:L61](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L61)
```
contracts/lib/EIP712.sol:61:            abi.encode(
```
[EIP712.sol:L91](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L91)
```
contracts/lib/EIP712.sol:91:                abi.encode(
```
[EIP712.sol:L107](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L107)
```
contracts/lib/EIP712.sol:107:                abi.encode(nonce)
```
[EIP712.sol:L132](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L132)
```
contracts/lib/EIP712.sol:132:            keccak256(abi.encode(
```
[EIP712.sol:L147](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L147)
```
contracts/lib/EIP712.sol:147:            keccak256(abi.encode(
```
### Reference:

[https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked](https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked)

-----
## 10. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`

### Instances:
[BlurExchange.sol:L475](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475)
```
contracts/BlurExchange.sol:475:        uint256 totalFee = 0;
```
[ReentrancyGuarded.sol:L10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)
```
contracts/lib/ReentrancyGuarded.sol:10:    bool reentrancyLock = false;
```
### Recommendation:

I suggest removing explicit initializations for default values.


-----

## 11. Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

This results in the keccak operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas.

### Instances:
** PLEASE ADD LINK TO FILES INSTEAD OF CODE FOR THIS ONE.. **
[EIP712.sol:L20](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20)
```
contracts/lib/EIP712.sol:20:    bytes32 constant public FEE_TYPEHASH = keccak256(
```
[EIP712.sol:L23](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L23)
```
contracts/lib/EIP712.sol:23:    bytes32 constant public ORDER_TYPEHASH = keccak256(
```
[EIP712.sol:L26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L26)
```
contracts/lib/EIP712.sol:26:    bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
```
[EIP712.sol:L29](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L29)
```
contracts/lib/EIP712.sol:29:    bytes32 constant public ROOT_TYPEHASH = keccak256(
```
[EIP712.sol:L33](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L33)
```
contracts/lib/EIP712.sol:33:    bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```
### References:

[https://github.com/code-423n4/2021-10-slingshot-findings/issues/3](https://github.com/code-423n4/2021-10-slingshot-findings/issues/3)


-----

## 12. Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Instances:
[BlurExchange.sol:L318](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
```
contracts/BlurExchange.sol:318:            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
```
[BlurExchange.sol:L482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)
```
contracts/BlurExchange.sol:482:        require(totalFee <= price, "Total amount of fees are more than the price");
```
[ExecutionDelegate.sol:L22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
```
contracts/ExecutionDelegate.sol:22:        require(contracts[msg.sender], "Contract is not approved to make transfers");
```
### Recommendations:

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors as described next.

Custom errors from Solidity 0.8.4 are cheaper than revert strings
(cheaper deployment cost and runtime cost when the revert condition is
met)

Source [Custom Errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) in Solidity:


-----

## 13. Using bools for storage incurs overhead

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Instances:
[ReentrancyGuarded.sol:L10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)
```
contracts/lib/ReentrancyGuarded.sol:10:    bool reentrancyLock = false;
```
[BlurExchange.sol:L71](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L71)
```
contracts/BlurExchange.sol:71:    mapping(bytes32 => bool) public cancelledOrFilled;
```
[BlurExchange.sol:L421](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L421)
```
contracts/BlurExchange.sol:421:        bool canMatch;
```
[ExecutionDelegate.sol:L18](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L18)
```
contracts/ExecutionDelegate.sol:18:    mapping(address => bool) public contracts;
```
[ExecutionDelegate.sol:L19](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L19)
```
contracts/ExecutionDelegate.sol:19:    mapping(address => bool) public revokedApproval;
```
### References:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)


-----

## 14. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed

### Instances:

[OrderStructs.sol:L9](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol#L9)
```
contracts/lib/OrderStructs.sol:9:    uint16 rate;
```
[OrderStructs.sol:L32](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol#L32)
```
contracts/lib/OrderStructs.sol:32:    uint8 v;
```
[BlurExchange.sol:L347](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L347)
```
contracts/BlurExchange.sol:347:        uint8 v,
```
[BlurExchange.sol:L383](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L383)
```
contracts/BlurExchange.sol:383:        uint8 v; bytes32 r; bytes32 s;
```
[BlurExchange.sol:L403](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L403)
```
contracts/BlurExchange.sol:403:        uint8 v,
```
### References:

[https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead](https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)


-----
##  15. Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, 'zero address')
  revert(0x00, 0x20)
 }
}
```
### Instances
[BlurExchange.sol:L219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219)
```
contracts/BlurExchange.sol:219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
```
[BlurExchange.sol:L228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228)
```
contracts/BlurExchange.sol:228:        require(address(_policyManager) != address(0), "Address cannot be zero");
```
[BlurExchange.sol:L237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)
```
contracts/BlurExchange.sol:237:        require(_oracle != address(0), "Address cannot be zero");
```
[BlurExchange.sol:L265](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L265)
```
contracts/BlurExchange.sol:265:            (order.trader != address(0)) &&
```

-----


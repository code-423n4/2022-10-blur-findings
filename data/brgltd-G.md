# [01] Prefix increment costs less gas than postfix increment

Saves roughly 5 gas per loop.

There are 5 instances of this issue.

```
File: contracts/BlurExchange.sol
199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

```
File: contracts/PolicyManager.sol
77: for (uint256 i = 0; i < length; i++) {
```

```
File: contracts/lib/EIP712.sol
77: for (uint256 i = 0; i < fees.length; i++) {
```

```
File: contracts/lib/MerkleVerifier.sol
38: for (uint256 i = 0; i < proof.length; i++) {
```

# [02] The increment in for loop post condition can be made unchecked to save gas

It can save 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked).

There are 5 instances of this issue.

```
File: contracts/BlurExchange.sol
199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

```
File: contracts/PolicyManager.sol
77: for (uint256 i = 0; i < length; i++) {
```

```
File: contracts/lib/EIP712.sol
77: for (uint256 i = 0; i < fees.length; i++) {
```

```
File: contracts/lib/MerkleVerifier.sol
38: for (uint256 i = 0; i < proof.length; i++) {
```

# [03] Cache the length of the array before the loop

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

There are 4 instances of this issue.

```
File: contracts/BlurExchange.sol
199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

```
File: contracts/lib/EIP712.sol
77: for (uint256 i = 0; i < fees.length; i++) {
```

```
File: contracts/lib/MerkleVerifier.sol
38: for (uint256 i = 0; i < proof.length; i++) {
```

# [04] Initializing a variable with the default value wastes gas

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

There are 7 instances of this issue.

```
File: contracts/BlurExchange.sol
199: for (uint8 i = 0; i < orders.length; i++) {
475: uint256 totalFee = 0;
476: for (uint8 i = 0; i < fees.length; i++) {
```

```
File: contracts/PolicyManager.sol
77: for (uint256 i = 0; i < length; i++) {
```

```
File: contracts/lib/EIP712.sol
77: for (uint256 i = 0; i < fees.length; i++) {
```

```
File: contracts/lib/MerkleVerifier.sol
38: for (uint256 i = 0; i < proof.length; i++) {
```

```
File: contracts/lib/ReentrancyGuarded.sol
10: bool reentrancyLock = false;
```

# [05] Use != 0 instead of > 0 to save gas.

For unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

There's 1 instance of this issue.

```
File: contracts/BlurExchange.sol
557: return size > 0;
```

# [06] Use custom errors rather than require/revert strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas.

There are 24 instances of this issue.

```
File: contracts/BlurExchange.sol
36: require(isOpen == 1, "Closed");
139: require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140: require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
142: require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143: require(_validateSignatures(buy, buyHash), "Buy failed authorization");
219: require(address(_executionDelegate) != address(0), "Address cannot be zero");
228: require(address(_policyManager) != address(0), "Address cannot be zero");
237: require(_oracle != address(0), "Address cannot be zero");
318: require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
407: require(v == 27 || v == 28, "Invalid v parameter");
424: require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
428: require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
431: require(canMatch, "Orders cannot be matched");
482: require(totalFee <= price, "Total amount of fees are more than the price");
513: revert("Invalid payment token");
534: require(_exists(collection), "Collection does not exist");
```

```
File: contracts/ExecutionDelegate.sol
22: require(contracts[msg.sender], "Contract is not approved to make transfers");
77: require(revokedApproval[from] == false, "User has revoked approval");
92: require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```

```
File: contracts/PolicyManager.sol
26: require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
37: require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

```
File: contracts/lib/ReentrancyGuarded.sol
14: require(!reentrancyLock, "Reentrancy detected");
```

# [07] Don’t compare boolean expressions to boolean literals

if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

There are 5 instances of this issue.

```
File: contracts/BlurExchange.sol
267: (cancelledOrFilled[orderHash] == false) &&
```

```
File: contracts/ExecutionDelegate.sol
77: require(revokedApproval[from] == false, "User has revoked approval");
92: require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```

# [08] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

There are 3 instances of this issue.

```
File: contracts/BlurExchange.sol
57: string public constant name = "Blur Exchange";
58: string public constant version = "1.0";
59: uint256 public constant INVERSE_BASIS_POINT = 10000;
```

# [09] x += y costs more gas than x = x + y for state variables

Using the addition operator instead of plus-equals saves 113 [gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8).

There 1 instance with this issue.

```
File: contracts/BlurExchange.sol
208: nonces[msg.sender] += 1;
```

# [10] Avoid duplicated validation logic

Repeated validation statements can be refactored into a single function modifier and save gas.

```
File: contracts/ExecutionDelegate.sol
77: require(revokedApproval[from] == false, "User has revoked approval");
92: require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```

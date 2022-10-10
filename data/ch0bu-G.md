
## 1. `++i` costs less gas compared to `i++` or `i += 1`, same for `--i/i--`. Especially in for loops

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments i and returns the initial value of `i`.

```
uint i = 1;  
i++; // == 1 but i == 2
```
But ++i returns the actual incremented value:
```
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

I suggest using ++i instead of i++ to increment the value of an uint variable.

If done inside for loop, saves 6 gas per loop.

```
BlurExchange.sol
199      for (uint8 i = 0; i < orders.length; i++) {
476      for (uint8 i = 0; i < fees.length; i++) {
```
```
EIP712.sol
77       for (uint256 i = 0; i < fees.length; i++) {
```
```
MerkleVerifier.sol
38       for (uint256 i = 0; i < proof.length; i++) {
```
```
PolicyManager.sol
77       for (uint256 i = 0; i < length; i++) {
```

## 2. No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

```
BlurExchange.sol
199      for (uint8 i = 0; i < orders.length; i++) {
475      uint256 totalFee = 0;
476      for (uint8 i = 0; i < fees.length; i++) {
```
```
EIP712.sol
77       for (uint256 i = 0; i < fees.length; i++) {
```
```
MerkleVerifier.sol
38       for (uint256 i = 0; i < proof.length; i++) {
```
```
PolicyManager.sol
77       for (uint256 i = 0; i < length; i++) {
```
```
ReentrancyGuarded.sol
10       bool reentrancyLock = false;
```

## 3. Use custom errors instead of revert strings to save Gas


Custom errors from Solidity 0.8.4 are cheaper than revert string (cheaper deployment cost and runtime cost when the revert condition is met)

Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:

> Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

```
BlurExchange.sol
139		require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140             require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
142             require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143             require(_validateSignatures(buy, buyHash), "Buy failed authorization");
219		require(address(_executionDelegate) != address(0), "Address cannot be zero");
228		require(address(_policyManager) != address(0), "Address cannot be zero");
237		require(_oracle != address(0), "Address cannot be zero");
318		require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
407		require(v == 27 || v == 28, "Invalid v parameter");
424		require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
428		require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
431		require(canMatch, "Orders cannot be matched");
482		require(totalFee <= price, "Total amount of fees are more than the price");
534		require(_exists(collection), "Collection does not exist");
```
```
ExecutionDelegate.sol
22		require(contracts[msg.sender], "Contract is not approved to make transfers");
77		require(revokedApproval[from] == false, "User has revoked approval");
92		require(revokedApproval[from] == false, "User has revoked approval");
108		require(revokedApproval[from] == false, "User has revoked approval");
124		require(revokedApproval[from] == false, "User has revoked approval");
```
```
PolicyManager.sol
26		require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
37		require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```
```
ReentrancyGuarded.sol
14		require(!reentrancyLock, "Reentrancy detected");
```

## 4. Increments can be unchecked


In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. This saves 30-40 gas PER LOOP.

```
BlurExchange.sol
199      for (uint8 i = 0; i < orders.length; i++) {
476      for (uint8 i = 0; i < fees.length; i++) {
```
```
EIP712.sol
77       for (uint256 i = 0; i < fees.length; i++) {
```
```
MerkleVerifier.sol
38       for (uint256 i = 0; i < proof.length; i++) {
```
```
PolicyManager.sol
77       for (uint256 i = 0; i < length; i++) {
```

## 5. \<array>.length should not be looked up in every loop of a for-loop

The overheads outlined below are PER LOOP, excluding the first loop

- storage arrays incur a Gwarmaccess (100 gas)
- memory arrays use MLOAD (3 gas)
- calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP\<N> (3 gas), and gets rid of the extra DUP\<N> needed to store the stack offset


```
BlurExchange.sol
199      for (uint8 i = 0; i < orders.length; i++) {
476      for (uint8 i = 0; i < fees.length; i++) {
```
```
EIP712.sol
77       for (uint256 i = 0; i < fees.length; i++) {
```
```
MerkleVerifier.sol
38       for (uint256 i = 0; i < proof.length; i++) {
```

## 6. require()/revert() strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE (https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs 3 gas

```
ExecutionDelegate.sol
22       require(contracts[msg.sender], "Contract is not approved to make transfers");
```
```
BlurExchange.sol
482      require(totalFee <= price, "Total amount of fees are more than the price");
```

## 7. Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(!directValue) instead of if(directValue == false)

```
ExecutionDelegate.sol
77       require(revokedApproval[from] == false, "User has revoked approval");
92       require(revokedApproval[from] == false, "User has revoked approval");
108      require(revokedApproval[from] == false, "User has revoked approval");
124      require(revokedApproval[from] == false, "User has revoked approval");
```

## 8. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

## 9. Not scripted Add `unchecked {}` for substractions where the operands cannot underflow because of a previous `require()` or `if` statement


`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

```
BlurExchange.sol
485       uint256 receiveAmount = price - totalFee;
```

## 10. Using `bool`s for storage incurs overhead


// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and 
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past

```
BlurExchange.sol
421      bool canMatch;
```
```
ReentrancyGuarded.sol
10       bool reentrancyLock = false;
```

## 11. `>=` COSTS LESS GAS THAN `>` (AND `<=` CHEAPER THAN `<`)

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas (https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde).This also holds true between `<=` and `<`.

Consider replacing strict inequalities with non-strict ones to save some gas 

```
MerkleVerifier.sol
46	return a < b ? _efficientHash(a, b) : _efficientHash(b, a);
```
```
PolicyManager.sol
71	if (length > _whitelistedPolicies.length() - cursor) {
```

## 12. Using `private` rather than `public` for constants, saves gas


If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

```
EIP712.sol
20	bytes32 constant public FEE_TYPEHASH = keccak256(
23	bytes32 constant public ORDER_TYPEHASH = keccak256(
26	bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
29	bytes32 constant public ROOT_TYPEHASH = keccak256(
```
```
BlurExchange.sol
57	string public constant name = "Blur Exchange";
58	string public constant version = "1.0";
59	uint256 public constant INVERSE_BASIS_POINT = 10000;
```


## 13. Empty blocks should be removed or emit something


The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback()` payable functions that are not used, can be removed to save deployment gas.

```
BlurExchange.sol
53	function _authorizeUpgrade(address) internal override onlyOwner {}
```

## 14. `keccak256()`should only need to be called on a specific string literal once


It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once

```
EIP712.sol
20	bytes32 constant public FEE_TYPEHASH = keccak256(
21		"Fee(uint16 rate,address recipient)"
22	);
23	bytes32 constant public ORDER_TYPEHASH = keccak256(
24		"Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
25	);
26	bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
27		"OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
28	);
29	bytes32 constant public ROOT_TYPEHASH = keccak256(
30		"Root(bytes32 root)"
31	);
32
33	bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
34		"EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
35	);
```

## 15. Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`


See this issue for a detail description of the issue (https://github.com/ethereum/solidity/issues/9232)

```
EIP712.sol
20	bytes32 constant public FEE_TYPEHASH = keccak256(
23	bytes32 constant public ORDER_TYPEHASH = keccak256(
26	bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
29	bytes32 constant public ROOT_TYPEHASH = keccak256(
33	bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```

## 16. Not using the named return variables when a function returns, wastes deployment gas

```
ERC1967Proxy.sol
30	return ERC1967Upgrade._getImplementation();
```
```
PolicyManager.sol
48	return _whitelistedPolicies.contains(policy);
55	return _whitelistedPolicies.length();
67	returns (address[] memory, uint256)
81	return (whitelistedPolicies, cursor + length);
```
```
ExecutionDelegate.sol
125	return IERC20(token).transferFrom(from, to, amount);
```


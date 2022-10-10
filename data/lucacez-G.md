### DON'T INITIALIZE VARIABLES WITH DEFAULT VALUE
Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecesary gas.

There are 7 instances of this issue:

```
File: contracts\BlurExchange.sol

199:     for (uint8 i = 0; i < orders.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
```
File: contracts\BlurExchange.sol

475:     uint256 totalFee = 0;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475
```
File: contracts\BlurExchange.sol

476:     for (uint8 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476
```
File: contracts\PolicyManager.sol

77:     for (uint256 i = 0; i < length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77
```
File: contracts\lib\EIP712.sol

77:     for (uint256 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
```
File: contracts\lib\MerkleVerifier.sol

38:     for (uint256 i = 0; i < proof.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38
```
File: contracts\lib\ReentrancyGuarded.sol

10:     bool reentrancyLock = false;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10
### CACHE THE LENGTH OF ARRAYS IN LOOPS

The solidity compiler will always read the length of the array during each iteration. That is,

1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):

Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

File: EIP712.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
```
     for (uint256 i = 0; i < fees.length; i++) {
```
File: MerkleVerifier.sol [Line 38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
```
     for (uint256 i = 0; i < proof.length; i++) {
```
This optimization is especially important if it is a storage array as it’s our case here.

The above should be modified to
```
```

### COMPARISONS: != IS MORE EFFICIENT THAN > IN REQUIRE (6 GAS LESS)

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas).

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here:

File: BlurExchange.sol [Line 557](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L557)
```
     return size > 0;
```

### ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 IN FOR LOOPS (~5 GAS PER ITERATION)
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:
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

Instances include:

File: BlurExchange.sol [Line 199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
```
     for (uint8 i = 0; i < orders.length; i++) {
```

File: BlurExchange.sol [Line 476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
```
     for (uint8 i = 0; i < fees.length; i++) {
```

File: PolicyManager.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
```
     for (uint256 i = 0; i < length; i++) {
```

File: EIP712.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
```
     for (uint256 i = 0; i < fees.length; i++) {
```

File: MerkleVerifier.sol [Line 38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
```
     for (uint256 i = 0; i < proof.length; i++) {
```

### USING UNCHECKED BLOCKS TO SAVE GAS - INCREMENTS IN FOR LOOP CAN BE UNCHECKED ( SAVE 30-40 GAS PER LOOP ITERATION)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let’s work with a sample loop below.
```
for(uint256 i; i < 10; i++){
//doSomething
}
```
can be written as shown below.
```
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```
We can also write it as an inlined function like below.
```
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```
Affected code
File: PolicyManager.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
```
     for (uint256 i = 0; i < length; i++) {
                 whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
             }
```
The above should be modified to:
```
     for (uint256 i = 0; i < length;) {
                whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
		unchecked {
			++i;
		}
             }
```
File: EIP712.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
```
     for (uint256 i = 0; i < fees.length; i++) {
```
File: MerkleVerifier.sol [Line 38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
```
     for (uint256 i = 0; i < proof.length; i++) {
```

### USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

File: BlurExchange.sol [Line 36](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L36)
```
     require(isOpen == 1, "Closed");
```

File: BlurExchange.sol [Line 134](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134)
```
     require(sell.order.side == Side.Sell);
```

File: BlurExchange.sol [Line 139](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139)
```
     require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
```

File: BlurExchange.sol [Line 140](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L140)
```
     require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
```

File: BlurExchange.sol [Line 142](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L142)
```
     require(_validateSignatures(sell, sellHash), "Sell failed authorization");
```

File: BlurExchange.sol [Line 143](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L143)
```
     require(_validateSignatures(buy, buyHash), "Buy failed authorization");
```

File: BlurExchange.sol [Line 183](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L183)
```
     require(msg.sender == order.trader);
```

File: BlurExchange.sol [Line 219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219)
```
     require(address(_executionDelegate) != address(0), "Address cannot be zero");
```

File: BlurExchange.sol [Line 228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228)
```
     require(address(_policyManager) != address(0), "Address cannot be zero");
```

File: BlurExchange.sol [Line 237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)
```
     require(_oracle != address(0), "Address cannot be zero");
```

File: BlurExchange.sol [Line 318](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
```
     require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
```

File: BlurExchange.sol [Line 407](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407)
```
     require(v == 27 || v == 28, "Invalid v parameter");
```

File: BlurExchange.sol [Line 424](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424)
```
     require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
```

File: BlurExchange.sol [Line 428](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428)
```
     require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
```

File: BlurExchange.sol [Line 431](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L431)
```
     require(canMatch, "Orders cannot be matched");
```

File: BlurExchange.sol [Line 452](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452)
```
     require(msg.value == price);
```

File: BlurExchange.sol [Line 482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)
```
     require(totalFee <= price, "Total amount of fees are more than the price");
```

File: BlurExchange.sol [Line 534](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534)
```
     require(_exists(collection), "Collection does not exist");
```

FIle: ExecutionDelegate.sol [Line 22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
```
     require(contracts[msg.sender], "Contract is not approved to make transfers");
```

FIle: ExecutionDelegate.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
```
     require(revokedApproval[from] == false, "User has revoked approval");
```

FIle: ExecutionDelegate.sol [Line 92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
```
     require(revokedApproval[from] == false, "User has revoked approval");
```

FIle: ExecutionDelegate.sol [Line 108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
```
     require(revokedApproval[from] == false, "User has revoked approval");
```

FIle: ExecutionDelegate.sol [Line 124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)
```
     require(revokedApproval[from] == false, "User has revoked approval");
```

File: PolicyManager [Line 26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26)
```
     require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
```

File: PolicyManager [Line 37](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)
```
     require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

File: ReentrancyGuarded.sol [Line 14](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L14)
```
     require(!reentrancyLock, "Reentrancy detected");
```

### USING BOOLS FOR STORAGE INCURS OVERHEAD
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

Instances affected include
File: BlurExchange.sol [Line 421](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L421)
```
     bool canMatch;
```

File: ReentrancyGuarded.sol [Line 10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)
```
bool reentrancyLock = false;
```

### USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

File: BlurExchange.sol [Line 57](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57)
```
     string public constant name = "Blur Exchange";
```

File: BlurExchange.sol [Line 58](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L58)
```
     string public constant version = "1.0";
```

File: BlurExchange.sol [Line 59](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L59)
```
     uint256 public constant INVERSE_BASIS_POINT = 10000;
```

File: EIP712.sol [Line 20](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20)
```
     bytes32 constant public FEE_TYPEHASH = keccak256(
```

File: EIP712.sol [Line 23](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L23)
```
     bytes32 constant public ORDER_TYPEHASH = keccak256(
```

File: EIP712.sol [Line 26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L26)
```
     bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
```

File: EIP712.sol [Line 29](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L29)
```
     bytes32 constant public ROOT_TYPEHASH = keccak256(
```

File: EIP712.sol [Line 33](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L33)
```
     bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```
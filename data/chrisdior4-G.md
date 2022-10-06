## USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS 
 # Custom errors are more gas efficient than using require with a string explanation. So ideally you'd always use this over require.


There are 22 instances of this issue:
 ================= 
### file: BlurExchange.sol

1.require(isOpen == 1, "Closed");

2.require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
3.require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

4.require(_validateSignatures(sell, sellHash), "Sell failed authorization");
5.require(_validateSignatures(buy, buyHash), "Buy failed authorization");

6.require(address(_executionDelegate) != address(0), "Address cannot be zero");
7.require(address(_policyManager) != address(0), "Address cannot be zero");
8.require(_oracle != address(0), "Address cannot be zero");

9.require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
10.require(v == 27 || v == 28, "Invalid v parameter");

11.require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
12.require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

13.require(canMatch, "Orders cannot be matched");
14.require(totalFee <= price, "Total amount of fees are more than the price");
15.require(_exists(collection), "Collection does not exist"); 



1.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L36 2.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139
3.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L140
4.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L142
5.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L143
6.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219
7.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228
8.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237
9.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318
10.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407
11.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424
12.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428
13.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L431
14.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482
15.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534

========================

### file: ExecutionDelegate.sol 

16.require(contracts[msg.sender], "Contract is not approved to make transfers");
17.require(revokedApproval[from] == false, "User has revoked approval");
18.require(revokedApproval[from] == false, "User has revoked approval");
19.require(revokedApproval[from] == false, "User has revoked approval");
20.require(revokedApproval[from] == false, "User has revoked approval");  


16.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22
17.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77
18.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92
19.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108
20.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124  

============================  

### file: PolicyManager.sol  

21.require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
22.require(_whitelistedPolicies.contains(policy), "Not whitelisted");  

21.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26
22.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37

 ============================  

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP   

# Reading array length at each iteration of the loop consumes more gas than necessary. In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

There are 4 instances of this issue:

### file: BlurExchange.so

l   ======================  

1.for (uint8 i = 0; i < fees.length; i++)  
2.for (uint8 i = 0; i < orders.length; i++)   

1.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476 2.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199    

Should be:  

1.uint256 feesLength = fees.length;     for (uint8 i = 0; i < feesLength;  ++i)  
2.uint256 ordersLength = orders.length;     for (uint8 i = 0; i < ordersLength; ++i)

  =======================  

### file: MerkleVerifier.sol  

3.for (uint256 i = 0; i < proof.length; i++) 

  3.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38  3. 

Should be:

3.uint256 proofLength = proof.length;    
   for (uint256 i = 0; i < proofLength; ++i)

   ======================== 

### file:  EIP712.sol  

4.for (uint256 i = 0; i < fees.length; i++) {

  4.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77  

Should be: 

4.uint256 feesLength = fees.length;     for (uint256 i = 0; i < feesLength; ++i)

    ==========================   

## ++i  COSTS LESS GAS THAN i++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS

  There are 5 instances of this issue:

 ### file: BlurExchange.sol 

======================  

1.for (uint8 i = 0; i < fees.length; i++)  
2.for (uint8 i = 0; i < orders.length; i++)   

1. https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476 2.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199    

Should be:  

1.for (uint8 i = 0; i < fees.length; ++i) 
2.for (uint8 i = 0; i < ordersLength; ++i)

  =========================  

### file: PolicyManager.sol  

3.for (uint256 i = 0; i < length; i++)  

3.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77  

Should be:   

3.for (uint256 i = 0; i < length; ++i)  

  =========================  

### file: MerkleVerifier.sol  

4.for (uint256 i = 0; i < proof.length; i++)   

4.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38  

Should be:  

4.for (uint256 i = 0; i < proof.length; ++i)

   ==========================  

### file:  EIP712.sol  

5.for (uint256 i = 0; i < fees.length; i++) {

  5.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77  

Should be:  

5. for (uint256 i = 0; i < fees.length; ++i) {  

==========================  

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES  

There are 2 instances of this issue:
 ================= 

file: BlurExchange.sol  

1.nonces[msg.sender] += 1; 
2.totalFee += fee;  

1.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208 2.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L479  

Should be:  

1.nonces[msg.sender] = nonces[msg.sender] + 1; 
2.totalFee = totalFee + fee;

  ==================   

## CONSTANTS CAN BE PRIVATE

# Marking constants as private save gas upon deployment, as the compiler does not have to create getter functions for these variables. It is worth noting that a private variable can still be read using either the verified contract source code or the bytecode. This may affect readability so this is left at the team’s discretion.

  There are 3 instances of this issue:

 ### file: BlurExchange.so

l ======================   

1.string public constant name = "Blur Exchange";
2.string public constant version = "1.0";
3.uint256 public constant INVERSE_BASIS_POINT = 10000;  

1.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57 2.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L58 3.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L59  

==================
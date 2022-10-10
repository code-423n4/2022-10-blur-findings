# Report
## Gas Optimizations ## 

### [G-01]: X += Y (X -= Y) costs more gas than X = X + Y (X = X - Y)
**Context:** 

1. ``` nonces[msg.sender] += 1; ``` [L208](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208)
2. ``` totalFee += fee; ``` [L479](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479)

**Recommendation:**

Change X += Y (X -= Y) to X = X + Y (X = X - Y).

### [G-02]: Don't initialize variable with its default value
**Context:** 

1. ``` bool reentrancyLock = false; ``` [L10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)
2. ``` for (uint256 i = 0; i < length; i++) { ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
3. ``` for (uint256 i = 0; i < fees.length; i++) { ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
4. ``` for (uint8 i = 0; i < orders.length; i++) { ``` [L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
5. ``` uint256 totalFee = 0; ``` [L475](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475)
6. ``` for (uint8 i = 0; i < fees.length; i++) { ``` [L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
7. ``` for (uint256 i = 0; i < proof.length; i++) { ``` [L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

**Description:**

Default value of uint is 0 and bool is false. It's unnecessary and costs more gas to initialize uint variavles to 0 and bool variables to false.

**Recommendation:**

1. Change ``` bool reentrancyLock = false; ``` to ``` bool reentrancyLock; ```.
2. Change ``` for (uint256 i = 0; i < length; i++) { ``` to ``` for (uint256 i; i < length; i++) { ```. 
3. Change  ``` for (uint256 i = 0; i < fees.length; i++) { ``` to  ``` for (uint256 i; i < fees.length; i++) { ```.
4. Change ``` for (uint8 i = 0; i < orders.length; i++) { ``` to ``` for (uint8 i; i < orders.length; i++) { ```.
5. Change ``` uint256 totalFee = 0; ``` to ``` uint256 totalFee; ```.
6. Change  ``` for (uint8 i = 0; i < fees.length; i++) { ``` to  ``` for (uint8 i; i < fees.length; i++) { ```.
7. Change ``` for (uint256 i = 0; i < proof.length; i++) { ``` to ``` for (uint256 i; i < proof.length; i++) { ```.


### [G-03]: i++ costs more gas than ++i
**Context:**

1. ``` for (uint256 i = 0; i < length; i++) { ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
2. ``` for (uint256 i = 0; i < fees.length; i++) { ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
3. ``` for (uint8 i = 0; i < orders.length; i++) { ``` [L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
4. ``` for (uint8 i = 0; i < fees.length; i++) { ``` [L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
5. ``` for (uint256 i = 0; i < proof.length; i++) { ``` [L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

**Recommendation:**

Change i++ to ++i.

### [G-04]: Use new variable instead of reading array length in every loop of a for-loop
**Context:**

1. ``` for (uint256 i = 0; i < fees.length; i++) { ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
2. ``` for (uint8 i = 0; i < orders.length; i++) { ``` [L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
3. ``` for (uint8 i = 0; i < fees.length; i++) { ``` [L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
4. ``` for (uint256 i = 0; i < proof.length; i++) { ``` [L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

**Description:**

If you read the length of the array at each iteration of the loop, this consumes a lot of gas.

**Recommendation:**

Store the array’s length in a variable before the for-loop, and use this new variable in the loop.


### [G-05]: Use custom errors instead of revert strings

[Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) are more gas efficient than using require with a string explanation.

1. ``` require(!reentrancyLock, "Reentrancy detected"); ``` [L14](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14)
2. ``` require(!_whitelistedPolicies.contains(policy), "Already whitelisted"); ``` [L26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26)
3. ``` require(_whitelistedPolicies.contains(policy), "Not whitelisted"); ``` [L37](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37)
4. ``` require(contracts[msg.sender], "Contract is not approved to make transfers"); ``` [L22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)
5. ``` require(revokedApproval[from] == false, "User has revoked approval"); ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77)
6. ``` require(revokedApproval[from] == false, "User has revoked approval"); ``` [L92](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92)
7. ``` require(revokedApproval[from] == false, "User has revoked approval"); ``` [L108](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108)
8. ``` require(revokedApproval[from] == false, "User has revoked approval"); ``` [L124](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124)
9. All 19 require in [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)

### [G-06]: variable can be immutable
**Context:**

``` address public weth; ``` [L63](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63)

**Description:**

Variable is set in the constructor and never modified after that.

**Recommendation:**

It is more gas efficient to mark it as immutable.

### [G-07]: The increment in for loop post condition can be made unchecked
**Context:**

1. ``` for (uint256 i = 0; i < length; i++) { ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
2. ``` for (uint256 i = 0; i < fees.length; i++) { ``` [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
3. ``` for (uint8 i = 0; i < orders.length; i++) { ``` [L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
4. ``` for (uint8 i = 0; i < fees.length; i++) { ``` [L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
5. ``` for (uint256 i = 0; i < proof.length; i++) { ``` [L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

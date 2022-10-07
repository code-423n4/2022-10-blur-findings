  # Gas Optimizations

## Summary Of Findings:

|  | Issue 
-- | -- 
1 | Optimizing `open()` and `close()` functions in `BlurExchange.sol`
2 | `cancelledOrFilled` mapping can use `uint256` instead of `bool` to save gas
3 | Optimizing `incrementNonce()` method in `BlurExchange.sol` 
4 | Optmizing `_transferFees` function in `BlurExchange.sol` 

## Detailed Report on Gas Optimization Findings:

### 1. <ins>Optimizing `open()` and `close()` functions in `BlurExchange.sol`</ins>
The [open() and close()](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L43-L50) functions in `BlurExchange.sol` can save gas by modifying the value of `isOpen` when exchange is closed.

Right now `isOpen` shifts back and forth between 1 and 0. This means when the `open()` function is called the `isOpen` state variable is set to 1 from 0 (writing non-zero value to zero content location), which uses lot of gas.

To avoid this we can make it so that the value of `isOpen` shifts back and forth between 1 and 2.
 
 The following `diff` shows the mitigation:
```diff
diff --git "a/.\\orig.sol" "b/.\\modified.sol"
index ba7057b..c76e39a 100644
--- "a/.\\orig.sol"
+++ "b/.\\modified.sol"
@@ -45,7 +45,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
         emit Opened();
     }
     function close() external onlyOwner {
-        isOpen = 0;
+        isOpen = 2;  //@audit gas. make it to 2. to avoid writing to zero content location.
         emit Closed();
     }
```

The hardhat gas report showed that the gas usage of `open()` decreased from 51326 to 34136, showing a saving of 17190.
And the gas usage of `close()` increased from 29341 to 34138, showing a excess usage of 4797.
So the overall gas improvement is 17190 - 4797 = 12393.

[See this](https://eips.ethereum.org/EIPS/eip-1087#motivation) for the reason for this change.

### 2. <ins>`cancelledOrFilled` mapping in `BlurExchange.sol`can use `uint256` instead of `bool` to save gas</ins>

> Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

[Source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)

This is very similar to the first optimization. And we can expect it to save on an average around 10000(rounding it down from the 12393 from the previous result) gas per call. 

Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

**Mitigation:-**
[cancelledOrFilled mapping](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L71)
```solidity
// change this line:
mapping(bytes32 => bool) public cancelledOrFilled;
// into this:
mapping(bytes32 => uint256) public cancelledOrFilled;
```

Note that this change will require several other changes throughout the codebase.

### 3. <ins>Optimizing `incrementNonce()` method in `BlurExchange.sol`</ins>

[incrementNonce() function](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L207-L210)

The optimizations are shown below with comments.

```diff
diff --git "a/.\\orig.sol" "b/.\\modified.sol"
index ba7057b..aecb728 100644
--- "a/.\\orig.sol"
+++ "b/.\\modified.sol"
@@ -205,8 +205,11 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
      * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
      */
     function incrementNonce() external {
-        nonces[msg.sender] += 1;
-        emit NonceIncremented(msg.sender, nonces[msg.sender]);
+        uint256 nonce = nonces[msg.sender];  //cache the state variable.
+        unchecked {  //practically will never overflow
+            nonces[msg.sender] = ++nonce;
+        }
+        emit NonceIncremented(msg.sender, nonce); //emit cached value
     }
```

The hardhat report showed that the above change reduced the average gas usage from 41379 to 41154 = 225 gas saved.

### 4. <ins>Optmizing `_transferFees` function in `BlurExchange.sol`</ins>
The following optimizations could be applied in [_transferFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L469-L487) function.
- Use `uint256` instead of `uint8` for loop index `i`.
- Cache length of `fees` array instead of checking it in every iteration.
- Instead of checking the `i < fees.length` condition inside the for loop brackets, do it inside the {}, as: `if(i == len) break;`
- Use unchecked arithmetics on loop index increment and turn `i++` to `++i`.
- Use unchecked arithmetics on `fee` calculation.
- Use unchecked arithmetics on addition of `fee` to `totalFee`. As practically it won't overflow as we are sending real tokens just in the line above it( which would revert if fee is too high)
- Convert `totalFee += fee` to `totalFee = totalFee + fee`
- Use unchecked arithmetics on calculation of `receiveAmount` due to the previous `require` statement.

The diff file shows the mitigations:

```diff
diff --git "a/.\\orig.sol" "b/.\\modified.sol"
index ba7057b..4ed2a17 100644
--- "a/.\\orig.sol"
+++ "b/.\\modified.sol"
@@ -473,16 +473,27 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
         uint256 price
     ) internal returns (uint256) {
         uint256 totalFee = 0;
-        for (uint8 i = 0; i < fees.length; i++) {
-            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
+        uint len = fees.length;
+        for (uint256 i = 0; ; ) {
+            if(i == len) break;
+            uint256 fee;
+            unchecked{
+                fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
+            }
             _transferTo(paymentToken, from, fees[i].recipient, fee);
-            totalFee += fee;
+            unchecked{
+                totalFee = totalFee + fee;
+                ++i;
+            }
         }
 
         require(totalFee <= price, "Total amount of fees are more than the price");
 
         /* Amount that will be received by seller. */
-        uint256 receiveAmount = price - totalFee;
+        uint256 receiveAmount;
+        unchecked {
+            receiveAmount = price - totalFee;   
+        }
         return (receiveAmount);
     }
```

The hardhat report showed that the above change reduced the average gas usage of `execute` function from  272762 to 272401 = 361 gas saved.

## Conclusions:
|  | Issue | Avg Gas Saved
-- | -- | -- 
1 | Optimizing `open()` and `close()` functions in `BlurExchange.sol` | 12393
2 | `cancelledOrFilled` mapping in `BlurExchange.sol`can use `uint256` instead of `bool` to save gas | 10000
3 | Optimizing `incrementNonce()` method in `BlurExchange.sol` | 225
4 | Optmizing `_transferFees` function in `BlurExchange.sol` | 361

### TOTAL GAS SAVED = 12393 + 10000 + 225 + 361 = <ins>22979</ins>.
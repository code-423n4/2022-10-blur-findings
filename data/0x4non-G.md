# gas

## Remove `weth` of `BlurExchange` and use it on `ExecutionDelegate`

The exchange will always use WETH (ask toad previously) so my recommendation would be remove `weth` var on [BlurExchange.sol#L63](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63) and create a immutable weth var on [ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol) then create a function that only transfer `WETH` instead of any token, check;
[ExecutionDelegate.sol#L125](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125)

## Pack struct `Order` could be packed
If you use a `uint40` for timestamps (which is safe) and pack with `side` you could take advantage and pack all in one slot.
```diff
--- a/contracts/lib/OrderStructs.sol
+++ b/contracts/lib/OrderStructs.sol
@@ -13,15 +13,14 @@ struct Fee {
 struct Order {
     address trader;
     Side side;
+    uint40 listingTime;
+    uint40 expirationTime;
     address matchingPolicy;
     address collection;
     uint256 tokenId;
     uint256 amount;
     address paymentToken;
     uint256 price;
-    uint256 listingTime;
-    /* Order expiration timestamp - 0 for oracle cancellations. */
-    uint256 expirationTime;
     Fee[] fees;
     uint256 salt;
     bytes extraParams;
```

## Use `unchecked` to do math operation when its impossible to over or underflow

In [BlurExchange.sol#L485-L486](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L485-L486);
```solidity
     require(totalFee <= price, "Total amount of fees are more than the price");
     // safe to use unchecked due require totalFee <= price
     unchecked {
        uint256 receiveAmount = price - totalFee;
        return (receiveAmount);
     }
```

In [BlurExchange.sol#L208](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208);
```
unchecked { nonces[msg.sender] += 1; }
```


## Use `++i` instead of `i++` to save gas, also use unchecked for loops
Instead of:
`for (uint256 i; i < length; i++) {`
Use this pattern;
```solidity
for (uint256 i; i < length;) {
     ... code ...
     unchecked { ++i; }
}
```

On;
- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
- [/BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
- [/PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
- [/lib/MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
- [/lib/EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

## Use `calldata` instead of `memory`
On [MerkleVerifier.sol#L35](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L35) `proof` can be `calldata` instead of memory

## Use custom errors for reverts
Use custom errors, which are cheaper at deployment than revert()/require() strings 
https://blog.soliditylang.org/2021/04/21/custom-errors/

## Cache expresion
You can cache expresion `_whitelistedPolicies.length() - cursor` on;
[PolicyManager.sol#L71-L72](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L71-L72)

## Optimize loop to avoid sum
On [PolicyManager.sol#L77-L79](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77-L79)
You could do something like this to save gas;
```diff
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -67,15 +67,17 @@ contract PolicyManager is IPolicyManager, Ownable {
         returns (address[] memory, uint256)
     {
         uint256 length = size;
+        uint256 _length = _whitelistedPolicies.length();

-        if (length > _whitelistedPolicies.length() - cursor) {
-            length = _whitelistedPolicies.length() - cursor;
+        if (length > _length) {
+            length = _length;
         }

         address[] memory whitelistedPolicies = new address[](length);

-        for (uint256 i = 0; i < length; i++) {
-            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
+        for (uint256 i = cursor; i < length;) {
+            whitelistedPolicies[i] = _whitelistedPolicies.at(i);
+            unchecked { ++i; }
         }

         return (whitelistedPolicies, cursor + length);
```
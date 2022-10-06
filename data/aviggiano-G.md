# 1. Optimize for loops
i) Do not set variable to zero
ii) Increment counter in unchecked block

**Occurrences**

```
$ git grep -n 'i++'

contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
```

**Optimization**

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..73e41c1 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -196,8 +196,12 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
      * @param orders Orders to cancel
      */
     function cancelOrders(Order[] calldata orders) external {
-        for (uint8 i = 0; i < orders.length; i++) {
+	uint8 i;
+        for (; i < orders.length; ) {
             cancelOrder(orders[i]);
+	    unchecked {
+		++i;
+	    }
         }
     }
 
@@ -473,10 +477,14 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
         uint256 price
     ) internal returns (uint256) {
         uint256 totalFee = 0;
-        for (uint8 i = 0; i < fees.length; i++) {
+	uint8 i;
+        for (; i < fees.length; ) {
             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
             _transferTo(paymentToken, from, fees[i].recipient, fee);
             totalFee += fee;
+	    unchecked {
+		++i;
+	    }
         }
 
         require(totalFee <= price, "Total amount of fees are more than the price");
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..44c0e0c 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -74,8 +74,12 @@ contract PolicyManager is IPolicyManager, Ownable {
 
         address[] memory whitelistedPolicies = new address[](length);
 
-        for (uint256 i = 0; i < length; i++) {
+	uint256 i;
+        for (; i < length; ) {
             whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
+	    unchecked {
+		++i;
+	    }
         }
 
         return (whitelistedPolicies, cursor + length);
diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..9911be6 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -74,8 +74,12 @@ contract EIP712 {
         bytes32[] memory feeHashes = new bytes32[](
             fees.length
         );
-        for (uint256 i = 0; i < fees.length; i++) {
+	uint256 i;
+        for (; i < fees.length; ) {
             feeHashes[i] = _hashFee(fees[i]);
+	    unchecked {
+		++i;
+	    }
         }
         return keccak256(abi.encodePacked(feeHashes));
     }
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..8f9dc4c 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -35,9 +35,13 @@ library MerkleVerifier {
         bytes32[] memory proof
     ) public pure returns (bytes32) {
         bytes32 computedHash = leaf;
-        for (uint256 i = 0; i < proof.length; i++) {
+	uint256 i;
+        for (; i < proof.length; ) {
             bytes32 proofElement = proof[i];
             computedHash = _hashPair(computedHash, proofElement);
+	    unchecked {
+		++i;
+	    }
         }
         return computedHash;
     }
```
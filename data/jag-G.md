**[G-01] Use local variable instead of storage variable for event emit** 
```diff
contracts/BlurExchange.sol
@@ -205,8 +208,11 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
      * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
      */
     function incrementNonce() external {
-        nonces[msg.sender] += 1;
-        emit NonceIncremented(msg.sender, nonces[msg.sender]);
+        uint _nonce;
+        unchecked {
+            _nonce = ++nonces[msg.sender];
+        }
+        emit NonceIncremented(msg.sender, _nonce);
     }
 
 
@@ -218,7 +224,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
     {
         require(address(_executionDelegate) != address(0), "Address cannot be zero");
         executionDelegate = _executionDelegate;
-        emit NewExecutionDelegate(executionDelegate);
+        emit NewExecutionDelegate(_executionDelegate);
     }
 
     function setPolicyManager(IPolicyManager _policyManager)
@@ -227,7 +233,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
     {
         require(address(_policyManager) != address(0), "Address cannot be zero");
         policyManager = _policyManager;
-        emit NewPolicyManager(policyManager);
+        emit NewPolicyManager(_policyManager);
     }
 
     function setOracle(address _oracle)
@@ -236,7 +242,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
     {
         require(_oracle != address(0), "Address cannot be zero");
         oracle = _oracle;
-        emit NewOracle(oracle);
+        emit NewOracle(_oracle);
     }

```

**[G-02] No need to initialize variable to 0**
```diff
diff --git a/contracts/lib/ReentrancyGuarded.sol b/contracts/lib/ReentrancyGuarded.sol
index 8028889..b92aaa5 100644
--- a/contracts/lib/ReentrancyGuarded.sol
+++ b/contracts/lib/ReentrancyGuarded.sol
@@ -7,7 +7,9 @@ pragma solidity 0.8.13;
  */
 contract ReentrancyGuarded {
 
-    bool reentrancyLock = false;
+    //bool reentrancyLock = false;
+    bool reentrancyLock;
+
 
     /* Prevent a contract function from being reentrant-called. */
     modifier reentrancyGuard {
```

**[G-03] Optimize "for" loop.
a. Use pre increment,
b. Use unchecked for pre increment,
c. No need to initialize init variable to 0**
```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..6cf249a 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -196,8 +196,11 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
      * @param orders Orders to cancel
      */
     function cancelOrders(Order[] calldata orders) external {
-        for (uint8 i = 0; i < orders.length; i++) {
+        for (uint8 i; i < orders.length;) {
             cancelOrder(orders[i]);
+            unchecked {
+                ++i;
+            }
         }
     }

@@ -473,17 +479,21 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
         uint256 price
     ) internal returns (uint256) {
         uint256 totalFee = 0;
-        for (uint8 i = 0; i < fees.length; i++) {
+        for (uint8 i; i < fees.length;) {
             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
             _transferTo(paymentToken, from, fees[i].recipient, fee);
             totalFee += fee;
+            unchecked {
+                ++i;
+            }
         }
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..f97128f 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
-        for (uint256 i = 0; i < length; i++) {
-            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
-        }
+        unchecked {
+            for (uint256 i; i < size; ++i) {
+                whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
+            }

diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..52aa2e2 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -74,8 +74,11 @@ contract EIP712 {
         bytes32[] memory feeHashes = new bytes32[](
             fees.length
         );
-        for (uint256 i = 0; i < fees.length; i++) {
+        for (uint256 i; i < fees.length;) {
             feeHashes[i] = _hashFee(fees[i]);
+            unchecked {
+                ++i;
+            }
         }
         return keccak256(abi.encodePacked(feeHashes));
     }
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..859c768 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -35,9 +35,12 @@ library MerkleVerifier {
         bytes32[] memory proof
     ) public pure returns (bytes32) {
         bytes32 computedHash = leaf;
-        for (uint256 i = 0; i < proof.length; i++) {
+        for (uint256 i; i < proof.length;) {
             bytes32 proofElement = proof[i];
             computedHash = _hashPair(computedHash, proofElement);
+            unchecked {
+                ++i;
+            }
         }
         return computedHash;
     }
```

**[G-04] Extra variable not needed**
```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..f97128f 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -66,18 +66,21 @@ contract PolicyManager is IPolicyManager, Ownable {
         override
         returns (address[] memory, uint256)
     {
-        uint256 length = size;
+        //extra variable not needed
+        //uint256 length = size;
 
-        if (length > _whitelistedPolicies.length() - cursor) {
-            length = _whitelistedPolicies.length() - cursor;
+        if (size > _whitelistedPolicies.length() - cursor) {
+            size = _whitelistedPolicies.length() - cursor;
         }
 
-        address[] memory whitelistedPolicies = new address[](length);
+        address[] memory whitelistedPolicies = new address[](size);
 
-        for (uint256 i = 0; i < length; i++) {
-            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
-        }
+        unchecked {
+            for (uint256 i; i < size; ++i) {
+                whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
+            }
 
-        return (whitelistedPolicies, cursor + length);
+            return (whitelistedPolicies, cursor + size);
+        }
     }
 }
```

[**G-05]  Use unchecked wherever arithmetic operation does not under/over flow**
```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..6cf249a 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -205,8 +208,11 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
      * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
      */
     function incrementNonce() external {
-        nonces[msg.sender] += 1;
-        emit NonceIncremented(msg.sender, nonces[msg.sender]);
+        uint _nonce;
+        unchecked {
+            _nonce = ++nonces[msg.sender];
+        }
+        emit NonceIncremented(msg.sender, _nonce);
     }
@@ -473,17 +479,21 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
         uint256 price
     ) internal returns (uint256) {
         uint256 totalFee = 0;
-        for (uint8 i = 0; i < fees.length; i++) {
+        for (uint8 i; i < fees.length;) {
             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
             _transferTo(paymentToken, from, fees[i].recipient, fee);
             totalFee += fee;
+            unchecked {
+                ++i;
+            }
         }
 
         require(totalFee <= price, "Total amount of fees are more than the price");
 
         /* Amount that will be received by seller. */
-        uint256 receiveAmount = price - totalFee;
-        return (receiveAmount);
+        unchecked {
+            return price - totalFee;
+        }
     }
diff --git a/contracts/lib/ERC1967Proxy.sol b/contracts/lib/ERC1967Proxy.sol
index 9c87aee..18dc152 100644
--- a/contracts/lib/ERC1967Proxy.sol
+++ b/contracts/lib/ERC1967Proxy.sol
@@ -19,8 +19,10 @@ contract ERC1967Proxy is Proxy, ERC1967Upgrade {
      * function call, and allows initializating the storage of the proxy like a Solidity constructor.
      */
     constructor(address _logic, bytes memory _data) payable {
-        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
-        _upgradeToAndCall(_logic, _data, false);
+        unchecked {
+            assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
+            _upgradeToAndCall(_logic, _data, false);
+        }
     }
 
     /**

```
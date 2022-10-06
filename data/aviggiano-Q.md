# 1. `BlurExchange.sol:cancelOrder` executes silently if order is already cancelled or filled

The current implementation completes silently if the order is already cancelled or filled, without emitting any event or changing any state.
It is recommended to `require` if the order has not been `cancelledOrFilled` in order to alert the user that their transaction would have no action.

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..91d13aa 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -184,11 +184,11 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
 
         bytes32 hash = _hashOrder(order, nonces[order.trader]);
 
-        if (!cancelledOrFilled[hash]) {
-            /* Mark order as cancelled, preventing it from being matched. */
-            cancelledOrFilled[hash] = true;
-            emit OrderCancelled(hash);
-        }
+        require(!cancelledOrFilled[hash], "Order already cancelled or filled");
+
+        /* Mark order as cancelled, preventing it from being matched. */
+        cancelledOrFilled[hash] = true;
+        emit OrderCancelled(hash);
     }
 
     /**
```

# 2. Validate initialization parameters on BlurExchange

The misconfiguration of `BlurExchange` can lead to the malfunctioning of the exchange. The `initialize` parameters should be validated to be non-null:

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..e220876 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -101,6 +101,13 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
         uint _blockRange
     ) public initializer {
         __Ownable_init();
+
+	require(_weth != address(0), "Address cannot be zero");
+	require(address(_executionDelegate) != address(0), "Address cannot be zero");
+	require(address(_policyManager) != address(0), "Address cannot be zero");
+	require(_oracle != address(0), "Address cannot be zero");
+	require(_blockRange != 0, "Block range cannot be zero");
+
         isOpen = 1;
 
         DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({

```

# 3. Functions of interface `IMatchingPolicy` are redundant

The functions `canMatchMakerAsk` and `canMatchMakerBid` are redundant. 
Only one is necessary, since to match two orders, all parameters from the "maker" and "taker" must be equal, with the exception of the `order.side`.
By renaming the variable names to simply `a` and `b`, it is evident that the functions implemented in `StandardPolicyERC721` and `StandardPolicyERC1155` are actually the same:

```diff
diff --git a/contracts/matchingPolicies/StandardPolicyERC1155.sol b/contracts/matchingPolicies/StandardPolicyERC1155.sol
index e17cc5e..414d81d 100644
--- a/contracts/matchingPolicies/StandardPolicyERC1155.sol
+++ b/contracts/matchingPolicies/StandardPolicyERC1155.sol
@@ -9,7 +9,7 @@ import {IMatchingPolicy} from "../interfaces/IMatchingPolicy.sol";
  * @dev Policy for matching orders at a fixed price for a specific ERC1155 tokenId
  */
 contract StandardPolicyERC1155 is IMatchingPolicy {
-    function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid)
+    function canMatchMakerAsk(Order calldata a, Order calldata b)
         external
         pure
         override
@@ -22,20 +22,20 @@ contract StandardPolicyERC1155 is IMatchingPolicy {
         )
     {
         return (
-            (makerAsk.side != takerBid.side) &&
-            (makerAsk.paymentToken == takerBid.paymentToken) &&
-            (makerAsk.collection == takerBid.collection) &&
-            (makerAsk.tokenId == takerBid.tokenId) &&
-            (makerAsk.matchingPolicy == takerBid.matchingPolicy) &&
-            (makerAsk.price == takerBid.price),
-            makerAsk.price,
-            makerAsk.tokenId,
+            (a.side != b.side) &&
+                (a.paymentToken == b.paymentToken) &&
+                (a.collection == b.collection) &&
+                (a.tokenId == b.tokenId) &&
+                (a.matchingPolicy == b.matchingPolicy) &&
+                (a.price == b.price),
+            a.price,
+            a.tokenId,
             1,
             AssetType.ERC1155
         );
     }
 
-    function canMatchMakerBid(Order calldata makerBid, Order calldata takerAsk)
+    function canMatchMakerBid(Order calldata a, Order calldata b)
         external
         pure
         override
@@ -48,17 +48,16 @@ contract StandardPolicyERC1155 is IMatchingPolicy {
         )
     {
         return (
-            (makerBid.side != takerAsk.side) &&
-            (makerBid.paymentToken == takerAsk.paymentToken) &&
-            (makerBid.collection == takerAsk.collection) &&
-            (makerBid.tokenId == takerAsk.tokenId) &&
-            (makerBid.matchingPolicy == takerAsk.matchingPolicy) &&
-            (makerBid.price == takerAsk.price),
-            makerBid.price,
-            makerBid.tokenId,
+            (a.side != b.side) &&
+                (a.paymentToken == b.paymentToken) &&
+                (a.collection == b.collection) &&
+                (a.tokenId == b.tokenId) &&
+                (a.matchingPolicy == b.matchingPolicy) &&
+                (a.price == b.price),
+            a.price,
+            a.tokenId,
             1,
             AssetType.ERC1155
         );
     }
 }
-
diff --git a/contracts/matchingPolicies/StandardPolicyERC721.sol b/contracts/matchingPolicies/StandardPolicyERC721.sol
index 30c50e9..f56952c 100644
--- a/contracts/matchingPolicies/StandardPolicyERC721.sol
+++ b/contracts/matchingPolicies/StandardPolicyERC721.sol
@@ -9,7 +9,7 @@ import {IMatchingPolicy} from "../interfaces/IMatchingPolicy.sol";
  * @dev Policy for matching orders at a fixed price for a specific ERC721 tokenId
  */
 contract StandardPolicyERC721 is IMatchingPolicy {
-    function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid)
+    function canMatchMakerAsk(Order calldata a, Order calldata b)
         external
         pure
         override
@@ -22,20 +22,20 @@ contract StandardPolicyERC721 is IMatchingPolicy {
         )
     {
         return (
-            (makerAsk.side != takerBid.side) &&
-            (makerAsk.paymentToken == takerBid.paymentToken) &&
-            (makerAsk.collection == takerBid.collection) &&
-            (makerAsk.tokenId == takerBid.tokenId) &&
-            (makerAsk.matchingPolicy == takerBid.matchingPolicy) &&
-            (makerAsk.price == takerBid.price),
-            makerAsk.price,
-            makerAsk.tokenId,
+            (a.side != b.side) &&
+                (a.paymentToken == b.paymentToken) &&
+                (a.collection == b.collection) &&
+                (a.tokenId == b.tokenId) &&
+                (a.matchingPolicy == b.matchingPolicy) &&
+                (a.price == b.price),
+            a.price,
+            a.tokenId,
             1,
             AssetType.ERC721
         );
     }
 
-    function canMatchMakerBid(Order calldata makerBid, Order calldata takerAsk)
+    function canMatchMakerBid(Order calldata a, Order calldata b)
         external
         pure
         override
@@ -48,17 +48,16 @@ contract StandardPolicyERC721 is IMatchingPolicy {
         )
     {
         return (
-            (makerBid.side != takerAsk.side) &&
-            (makerBid.paymentToken == takerAsk.paymentToken) &&
-            (makerBid.collection == takerAsk.collection) &&
-            (makerBid.tokenId == takerAsk.tokenId) &&
-            (makerBid.matchingPolicy == takerAsk.matchingPolicy) &&
-            (makerBid.price == takerAsk.price),
-            makerBid.price,
-            makerBid.tokenId,
+            (a.side != b.side) &&
+                (a.paymentToken == b.paymentToken) &&
+                (a.collection == b.collection) &&
+                (a.tokenId == b.tokenId) &&
+                (a.matchingPolicy == b.matchingPolicy) &&
+                (a.price == b.price),
+            a.price,
+            a.tokenId,
             1,
             AssetType.ERC721
         );
     }
 }
-

```
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

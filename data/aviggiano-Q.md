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
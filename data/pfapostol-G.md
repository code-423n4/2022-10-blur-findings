##### Summary

Gas savings are estimated using the gas report of existing `yarn compile && REPORT_GAS=true yarn test ` tests (the sum of all deployment costs and the sum of the costs of calling all methods) and may vary depending on the implementation of the fix.
Some optimizations (mostly logical) cannot be scored with a exact gas quantity.

Gas Optimizations
|        | Issue                                                                                                                   | Instances | Estimated gas(deployments) | Estimated gas(method call) |
| :----: | :---------------------------------------------------------------------------------------------------------------------- | :-------: | :------------------------: | :------------------------: |
| **1**  | Use custom errors rather than revert()/require() strings to save gas                                                    |    26     |          282 389           |             49             |
| **2**  | Using `private` rather than `public` for constants, saves gas                                                           |     8     |           99 638           |             45             |
| **3**  | Duplicated require()/revert() checks should be refactored to a modifier or function                                     |     7     |           91 132           |            -169            |
| **4**  | Increment in loop can be unchecked                                                                                      |     5     |           37 105           |            589             |
| **5**  | `require()`/`revert()` strings longer than 32 bytes cost extra gas                                                      |     2     |           24 842           |             0              |
| **6**  | Using calldata instead of memory for read-only arguments in external functions saves gas                                |     2     |           19 891           |             0              |
| **7**  | Using bools for storage incurs overhead                                                                                 |     6     |           17 186           |            777             |
| **8**  | Unnecessary if statement                                                                                                |     3     |           16 191           |             4              |
| **9**  | `<x> = <x> + 1` even more efficient than increment.                                                                     |     5     |           9 654            |            -50             |
| **10** | `private` functions only called once can be inlined to save gas                                                         |     1     |           4 071            |             0              |
| **11** | Unchecking arithmetics operations that can't underflow/overflow                                                         |     2     |           3 894            |             65             |
| **12** | Do not emit storage variables, when stack available                                                                     |     5     |           2 988            |            144             |
| **13** | `++i` costs less gas than `i++`, especially when it's used in for-loops (`--i`/`i--` too)                               |     4     |           2 136            |             40             |
| **14** | State variables should be cached in stack variables rather than re-reading them from storage                            |     1     |           1 734            |             0              |
|  ↓↓↓   | General optimizations*                                                                                                  |    ↓↓↓    |            ↓↓↓             |            ↓↓↓             |
| **1**  | `<array>.length` should not be looked up in every loop of a for-loop                                                    |     4     |                            |                            |
| **2**  | It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied |     5     |                            |                            |
| **3**  | Don't compare boolean expressions to boolean literals                                                                   |     5     |                            |                            |
|        | **Overall gas saved**                                                                                                   |  **91**   |        **519 061**         |         **1 422**          |

**Total: 91 instances over 17 issues**

*General optimizations is a "best practices", that i can't estimate, because optimization is: 
1. highly depends on user input; OR
2. depends on optimizer settings; OR
3. logical.
---

#### 1. **Use custom errors rather than revert()/require() strings to save gas (26 instances)**

Deployements. Gas Saved: **282 389**

Method Calls. Gas Saved: **49**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

##### - contracts/lib/ReentrancyGuarded.sol:[14](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L14)

```diff
diff --git a/contracts/lib/ReentrancyGuarded.sol b/contracts/lib/ReentrancyGuarded.sol
index 8028889..42ff439 100644
--- a/contracts/lib/ReentrancyGuarded.sol
+++ b/contracts/lib/ReentrancyGuarded.sol
@@ -7,11 +7,13 @@ pragma solidity 0.8.13;
    7,   7:  */
    8,   8: contract ReentrancyGuarded {
    9,   9: 
+       10:+    error ReentrancyDetected();
+       11:+
   10,  12:     bool reentrancyLock = false;
   11,  13: 
   12,  14:     /* Prevent a contract function from being reentrant-called. */
   13,  15:     modifier reentrancyGuard {
-  14     :-        require(!reentrancyLock, "Reentrancy detected");
+       16:+        if(reentrancyLock) revert ReentrancyDetected();
   15,  17:         reentrancyLock = true;
   16,  18:         _;
   17,  19:         reentrancyLock = false;
```

##### - contracts/BlurExchange.sol:[36](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L36), [134](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134), [139](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139), [140](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L140), [142](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L142), [143](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L143), [183](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L183), [219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219), [228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228), [237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237), [318](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318), [407](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407), [424](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424), [428](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428), [431](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L431), [452](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452), [482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482), [534](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..323c163 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -28,12 +28,25 @@ import {
   28,  28:  * @dev Core Blur exchange contract
   29,  29:  */
   30,  30: contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {
+       31:+    error CollectionNotExists();
+       32:+    error FeeMoreThanPrice();
+       33:+    error OrdersCannotMatch();
+       34:+    error PolicyNotWhitelisted();
+       35:+    error InvalidVParameter();
+       36:+    error BlockNumberOutOfRange();
+       37:+    error ZeroAddress();
+       38:+    error BuyFailedAuth();
+       39:+    error SellFailedAuth();
+       40:+    error BuyInvalidParameters();
+       41:+    error SellInvalidParameters();
+       42:+    error IsClosed();
+       43:+
   31,  44: 
   32,  45:     /* Auth */
   33,  46:     uint256 public isOpen;
   34,  47: 
   35,  48:     modifier whenOpen() {
-  36     :-        require(isOpen == 1, "Closed");
+       49:+        if(isOpen == 0) revert IsClosed();
   37,  50:         _;
   38,  51:     }
   39,  52: 
@@ -131,16 +144,16 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  131, 144:         reentrancyGuard
  132, 145:         whenOpen
  133, 146:     {
- 134     :-        require(sell.order.side == Side.Sell);
+      147:+        if(sell.order.side != Side.Sell) revert();
  135, 148: 
  136, 149:         bytes32 sellHash = _hashOrder(sell.order, nonces[sell.order.trader]);
  137, 150:         bytes32 buyHash = _hashOrder(buy.order, nonces[buy.order.trader]);
  138, 151: 
- 139     :-        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
- 140     :-        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
+      152:+        if(!_validateOrderParameters(sell.order, sellHash)) revert SellInvalidParameters();
+      153:+        if(!_validateOrderParameters(buy.order, buyHash)) revert BuyInvalidParameters();
  141, 154: 
- 142     :-        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
- 143     :-        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
+      155:+        if(!_validateSignatures(sell, sellHash)) revert SellFailedAuth();
+      156:+        if(!_validateSignatures(buy, buyHash)) revert BuyFailedAuth();
  144, 157: 
  145, 158:         (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType) = _canMatchOrders(sell.order, buy.order);
  146, 159: 
@@ -180,7 +193,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  180, 193:      */
  181, 194:     function cancelOrder(Order calldata order) public {
  182, 195:         /* Assert sender is authorized to cancel order. */
- 183     :-        require(msg.sender == order.trader);
+      196:+        if(msg.sender != order.trader) revert();
  184, 197: 
  185, 198:         bytes32 hash = _hashOrder(order, nonces[order.trader]);
  186, 199: 
@@ -216,7 +229,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  216, 229:         external
  217, 230:         onlyOwner
  218, 231:     {
- 219     :-        require(address(_executionDelegate) != address(0), "Address cannot be zero");
+      232:+        if(address(_executionDelegate) == address(0)) revert ZeroAddress();
  220, 233:         executionDelegate = _executionDelegate;
  221, 234:         emit NewExecutionDelegate(executionDelegate);
  222, 235:     }
@@ -225,7 +238,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  225, 238:         external
  226, 239:         onlyOwner
  227, 240:     {
- 228     :-        require(address(_policyManager) != address(0), "Address cannot be zero");
+      241:+        if(address(_policyManager) == address(0)) revert ZeroAddress();
  229, 242:         policyManager = _policyManager;
  230, 243:         emit NewPolicyManager(policyManager);
  231, 244:     }
@@ -234,7 +247,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  234, 247:         external
  235, 248:         onlyOwner
  236, 249:     {
- 237     :-        require(_oracle != address(0), "Address cannot be zero");
+      250:+        if(_oracle == address(0)) revert ZeroAddress();
  238, 251:         oracle = _oracle;
  239, 252:         emit NewOracle(oracle);
  240, 253:     }
@@ -315,7 +328,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  315, 328: 
  316, 329:         if (order.order.expirationTime == 0) {
  317, 330:             /* Check oracle authorization. */
- 318     :-            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
+      331:+            if(block.number - order.blockNumber >= blockRange) revert BlockNumberOutOfRange();
  319, 332:             if (
  320, 333:                 !_validateOracleAuthorization(
  321, 334:                     orderHash,
@@ -404,7 +417,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  404, 417:         bytes32 r,
  405, 418:         bytes32 s
  406, 419:     ) internal pure returns (address) {
- 407     :-        require(v == 27 || v == 28, "Invalid v parameter");
+      420:+        if(v != 27 && v != 28) revert InvalidVParameter();
  408, 421:         return ecrecover(digest, v, r, s);
  409, 422:     }
  410, 423: 
@@ -421,14 +434,14 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  421, 434:         bool canMatch;
  422, 435:         if (sell.listingTime <= buy.listingTime) {
  423, 436:             /* Seller is maker. */
- 424     :-            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
+      437:+            if(!policyManager.isPolicyWhitelisted(sell.matchingPolicy)) revert PolicyNotWhitelisted();
  425, 438:             (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy);
  426, 439:         } else {
  427, 440:             /* Buyer is maker. */
- 428     :-            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
+      441:+            if(!policyManager.isPolicyWhitelisted(buy.matchingPolicy)) revert PolicyNotWhitelisted();
  429, 442:             (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell);
  430, 443:         }
- 431     :-        require(canMatch, "Orders cannot be matched");
+      444:+        if(!canMatch) revert OrdersCannotMatch();
  432, 445: 
  433, 446:         return (price, tokenId, amount, assetType);
  434, 447:     }
@@ -449,7 +462,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  449, 462:         uint256 price
  450, 463:     ) internal {
  451, 464:         if (paymentToken == address(0)) {
- 452     :-            require(msg.value == price);
+      465:+            if(msg.value != price) revert();
  453, 466:         }
  454, 467: 
  455, 468:         /* Take fee. */
@@ -479,7 +492,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  479, 492:             totalFee += fee;
  480, 493:         }
  481, 494: 
- 482     :-        require(totalFee <= price, "Total amount of fees are more than the price");
+      495:+        if(totalFee > price) revert FeeMoreThanPrice();
  483, 496: 
  484, 497:         /* Amount that will be received by seller. */
  485, 498:         uint256 receiveAmount = price - totalFee;
@@ -531,7 +544,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  531, 544:         AssetType assetType
  532, 545:     ) internal {
  533, 546:         /* Assert collection exists. */
- 534     :-        require(_exists(collection), "Collection does not exist");
+      547:+        if(!_exists(collection)) revert CollectionNotExists();
  535, 548: 
  536, 549:         /* Call execution delegate. */
  537, 550:         if (assetType == AssetType.ERC721) {
```

##### - contracts/ExecutionDelegate.sol:[22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22), [77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77), [92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92), [108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108), [124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)

```diff
diff --git a/contracts/ExecutionDelegate.sol b/contracts/ExecutionDelegate.sol
index bb5e5ca..0cc962b 100644
--- a/contracts/ExecutionDelegate.sol
+++ b/contracts/ExecutionDelegate.sol
@@ -15,11 +15,14 @@ import {IExecutionDelegate} from "./interfaces/IExecutionDelegate.sol";
   15,  15:  */
   16,  16: contract ExecutionDelegate is IExecutionDelegate, Ownable {
   17,  17: 
+       18:+    error UserRevokedApproval();
+       19:+    error ContractNotApproved();
+       20:+
   18,  21:     mapping(address => bool) public contracts;
   19,  22:     mapping(address => bool) public revokedApproval;
   20,  23: 
   21,  24:     modifier approvedContract() {
-  22     :-        require(contracts[msg.sender], "Contract is not approved to make transfers");
+       25:+        if(!contracts[msg.sender]) revert ContractNotApproved();
   23,  26:         _;
   24,  27:     }
   25,  28: 
@@ -74,7 +77,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   74,  77:         approvedContract
   75,  78:         external
   76,  79:     {
-  77     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       80:+        if(revokedApproval[from] == true) revert UserRevokedApproval();
   78,  81:         IERC721(collection).transferFrom(from, to, tokenId);
   79,  82:     }
   80,  83: 
@@ -89,7 +92,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   89,  92:         approvedContract
   90,  93:         external
   91,  94:     {
-  92     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       95:+        if(revokedApproval[from] == true) revert UserRevokedApproval();
   93,  96:         IERC721(collection).safeTransferFrom(from, to, tokenId);
   94,  97:     }
   95,  98: 
@@ -105,7 +108,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  105, 108:         approvedContract
  106, 109:         external
  107, 110:     {
- 108     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      111:+        if(revokedApproval[from] == true) revert UserRevokedApproval();
  109, 112:         IERC1155(collection).safeTransferFrom(from, to, tokenId, amount, "");
  110, 113:     }
  111, 114: 
@@ -121,7 +124,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  121, 124:         external
  122, 125:         returns (bool)
  123, 126:     {
- 124     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      127:+        if(revokedApproval[from] == true) revert UserRevokedApproval();
  125, 128:         return IERC20(token).transferFrom(from, to, amount);
  126, 129:     }
  127, 130: }
```

##### - contracts/PolicyManager.sol:[26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26), [37](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..513d47d 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -13,6 +13,9 @@ import {IPolicyManager} from "./interfaces/IPolicyManager.sol";
   13,  13: contract PolicyManager is IPolicyManager, Ownable {
   14,  14:     using EnumerableSet for EnumerableSet.AddressSet;
   15,  15: 
+       16:+    error NotWhitelisted();
+       17:+    error AlreadyWhitelisted();
+       18:+
   16,  19:     EnumerableSet.AddressSet private _whitelistedPolicies;
   17,  20: 
   18,  21:     event PolicyRemoved(address indexed policy);
@@ -23,7 +26,7 @@ contract PolicyManager is IPolicyManager, Ownable {
   23,  26:      * @param policy address of policy to add
   24,  27:      */
   25,  28:     function addPolicy(address policy) external override onlyOwner {
-  26     :-        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
+       29:+        if(_whitelistedPolicies.contains(policy)) revert AlreadyWhitelisted();
   27,  30:         _whitelistedPolicies.add(policy);
   28,  31: 
   29,  32:         emit PolicyWhitelisted(policy);
@@ -34,7 +37,7 @@ contract PolicyManager is IPolicyManager, Ownable {
   34,  37:      * @param policy address of policy to remove
   35,  38:      */
   36,  39:     function removePolicy(address policy) external override onlyOwner {
-  37     :-        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
+       40:+        if(!_whitelistedPolicies.contains(policy)) revert NotWhitelisted();
   38,  41:         _whitelistedPolicies.remove(policy);
   39,  42: 
   40,  43:         emit PolicyRemoved(policy);
```

---

#### 2. **Using `private` rather than `public` for constants, saves gas (8 instances)**

Deployements. Gas Saved: **99 638**

Method Calls. Gas Saved: **45**

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

##### - contracts/BlurExchange.sol:[57](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57), [58](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L58), [59](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L59)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..0fa674c 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -54,9 +54,9 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
   54,  54: 
   55,  55: 
   56,  56:     /* Constants */
-  57     :-    string public constant name = "Blur Exchange";
-  58     :-    string public constant version = "1.0";
-  59     :-    uint256 public constant INVERSE_BASIS_POINT = 10000;
+       57:+    string private constant name = "Blur Exchange";
+       58:+    string private constant version = "1.0";
+       59:+    uint256 private constant INVERSE_BASIS_POINT = 10000;
   60,  60: 
   61,  61: 
   62,  62:     /* Variables */
```

##### - contracts/lib/EIP712.sol:[20](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20),  [23](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L23),  [26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L26),  [29](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L29), [33](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L33)

```diff
diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..8c95f36 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -17,20 +17,20 @@ contract EIP712 {
   17,  17:     }
   18,  18: 
   19,  19:     /* Order typehash for EIP 712 compatibility. */
-  20     :-    bytes32 constant public FEE_TYPEHASH = keccak256(
+       20:+    bytes32 constant private FEE_TYPEHASH = keccak256(
   21,  21:         "Fee(uint16 rate,address recipient)"
   22,  22:     );
-  23     :-    bytes32 constant public ORDER_TYPEHASH = keccak256(
+       23:+    bytes32 constant private ORDER_TYPEHASH = keccak256(
   24,  24:         "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
   25,  25:     );
-  26     :-    bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
+       26:+    bytes32 constant private ORACLE_ORDER_TYPEHASH = keccak256(
   27,  27:         "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
   28,  28:     );
-  29     :-    bytes32 constant public ROOT_TYPEHASH = keccak256(
+       29:+    bytes32 constant private ROOT_TYPEHASH = keccak256(
   30,  30:         "Root(bytes32 root)"
   31,  31:     );
   32,  32: 
-  33     :-    bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
+       33:+    bytes32 constant private EIP712DOMAIN_TYPEHASH = keccak256(
   34,  34:         "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
   35,  35:     );
   36,  36: 
```

---

#### 3. **Duplicated require()/revert() checks should be refactored to a modifier or function (7 instances)**

Deployements. Gas Saved: **91 132**

Method Calls. Gas Saved: **-169**

##### - contracts/BlurExchange.sol:[219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219), [228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228), [237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..fc8097b 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -49,6 +49,10 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
   49,  49:         emit Closed();
   50,  50:     }
   51,  51: 
+       52:+    function isZeroAddress(address _a) private pure {
+       53:+        require(_a != address(0), "Address cannot be zero");
+       54:+    }
+       55:+
   52,  56:     // required by the OZ UUPS module
   53,  57:     function _authorizeUpgrade(address) internal override onlyOwner {}
   54,  58: 
@@ -216,7 +220,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  216, 220:         external
  217, 221:         onlyOwner
  218, 222:     {
- 219     :-        require(address(_executionDelegate) != address(0), "Address cannot be zero");
+      223:+        isZeroAddress(address(_executionDelegate));
  220, 224:         executionDelegate = _executionDelegate;
  221, 225:         emit NewExecutionDelegate(executionDelegate);
  222, 226:     }
@@ -225,7 +229,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  225, 229:         external
  226, 230:         onlyOwner
  227, 231:     {
- 228     :-        require(address(_policyManager) != address(0), "Address cannot be zero");
+      232:+        isZeroAddress(address(_policyManager));
  229, 233:         policyManager = _policyManager;
  230, 234:         emit NewPolicyManager(policyManager);
  231, 235:     }
@@ -234,7 +238,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  234, 238:         external
  235, 239:         onlyOwner
  236, 240:     {
- 237     :-        require(_oracle != address(0), "Address cannot be zero");
+      241:+        isZeroAddress(_oracle);
  238, 242:         oracle = _oracle;
  239, 243:         emit NewOracle(oracle);
  240, 244:     }
```

##### - contracts/ExecutionDelegate.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77), [92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92), [108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108), [124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)

```diff
diff --git a/contracts/ExecutionDelegate.sol b/contracts/ExecutionDelegate.sol
index bb5e5ca..b2ccab7 100644
--- a/contracts/ExecutionDelegate.sol
+++ b/contracts/ExecutionDelegate.sol
@@ -23,6 +23,10 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   23,  23:         _;
   24,  24:     }
   25,  25: 
+       26:+    function isRevoked(address from) private view {
+       27:+        require(revokedApproval[from] == false, "User has revoked approval");
+       28:+    }
+       29:+
   26,  30:     event ApproveContract(address indexed _contract);
   27,  31:     event DenyContract(address indexed _contract);
   28,  32: 
@@ -74,7 +78,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   74,  78:         approvedContract
   75,  79:         external
   76,  80:     {
-  77     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       81:+        isRevoked(from);
   78,  82:         IERC721(collection).transferFrom(from, to, tokenId);
   79,  83:     }
   80,  84: 
@@ -89,7 +93,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   89,  93:         approvedContract
   90,  94:         external
   91,  95:     {
-  92     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       96:+        isRevoked(from);
   93,  97:         IERC721(collection).safeTransferFrom(from, to, tokenId);
   94,  98:     }
   95,  99: 
@@ -105,7 +109,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  105, 109:         approvedContract
  106, 110:         external
  107, 111:     {
- 108     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      112:+        isRevoked(from);
  109, 113:         IERC1155(collection).safeTransferFrom(from, to, tokenId, amount, "");
  110, 114:     }
  111, 115: 
@@ -121,7 +125,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  121, 125:         external
  122, 126:         returns (bool)
  123, 127:     {
- 124     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      128:+        isRevoked(from);
  125, 129:         return IERC20(token).transferFrom(from, to, amount);
  126, 130:     }
  127, 131: }
```

---

#### 4. **Increment in loop can be unchecked (5 instances)**

Deployements. Gas Saved: **37 105**

Method Calls. Gas Saved: **589**

##### - contracts/BlurExchange.sol:[199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199), [476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..faf2b73 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -196,8 +196,11 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  196, 196:      * @param orders Orders to cancel
  197, 197:      */
  198, 198:     function cancelOrders(Order[] calldata orders) external {
- 199     :-        for (uint8 i = 0; i < orders.length; i++) {
+      199:+        for (uint8 i = 0; i < orders.length;) {
  200, 200:             cancelOrder(orders[i]);
+      201:+            unchecked {
+      202:+                i = i + 1;
+      203:+            }
  201, 204:         }
  202, 205:     }
  203, 206: 
@@ -473,10 +476,11 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  473, 476:         uint256 price
  474, 477:     ) internal returns (uint256) {
  475, 478:         uint256 totalFee = 0;
- 476     :-        for (uint8 i = 0; i < fees.length; i++) {
+      479:+        for (uint8 i = 0; i < fees.length;) {
  477, 480:             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
  478, 481:             _transferTo(paymentToken, from, fees[i].recipient, fee);
  479, 482:             totalFee += fee;
+      483:+            unchecked {i = i + 1;}
  480, 484:         }
  481, 485: 
  482, 486:         require(totalFee <= price, "Total amount of fees are more than the price");
```

##### - contracts/PolicyManager.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..7dc2e7f 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -74,8 +74,9 @@ contract PolicyManager is IPolicyManager, Ownable {
   74,  74: 
   75,  75:         address[] memory whitelistedPolicies = new address[](length);
   76,  76: 
-  77     :-        for (uint256 i = 0; i < length; i++) {
+       77:+        for (uint256 i = 0; i < length;) {
   78,  78:             whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
+       79:+            unchecked {i = i + 1;}
   79,  80:         }
   80,  81: 
   81,  82:         return (whitelistedPolicies, cursor + length);
```

##### - contracts/lib/EIP712.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

```diff
diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..0a163eb 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -74,8 +74,9 @@ contract EIP712 {
   74,  74:         bytes32[] memory feeHashes = new bytes32[](
   75,  75:             fees.length
   76,  76:         );
-  77     :-        for (uint256 i = 0; i < fees.length; i++) {
+       77:+        for (uint256 i = 0; i < fees.length;) {
   78,  78:             feeHashes[i] = _hashFee(fees[i]);
+       79:+            unchecked {i = i + 1;}
   79,  80:         }
   80,  81:         return keccak256(abi.encodePacked(feeHashes));
   81,  82:     }
```

##### -contracts/lib/MerkleVerifier.sol:[38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

```diff
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..298b7ee 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -35,9 +35,10 @@ library MerkleVerifier {
   35,  35:         bytes32[] memory proof
   36,  36:     ) public pure returns (bytes32) {
   37,  37:         bytes32 computedHash = leaf;
-  38     :-        for (uint256 i = 0; i < proof.length; i++) {
+       38:+        for (uint256 i = 0; i < proof.length;) {
   39,  39:             bytes32 proofElement = proof[i];
   40,  40:             computedHash = _hashPair(computedHash, proofElement);
+       41:+            unchecked {i = i + 1;}
   41,  42:         }
   42,  43:         return computedHash;
   43,  44:     }
```

---

#### 5. **`require()`/`revert()` strings longer than 32 bytes cost extra gas (2 instances)**

Deployements. Gas Saved: **24 842**

Method Calls. Gas Saved: **0**

Each extra chunk of bytes past the original 32 incurs an MSTORE which costs 3 gas

##### - contracts/BlurExchange.sol:[482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..0d253ac 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -479,7 +479,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  479, 479:             totalFee += fee;
  480, 480:         }
  481, 481: 
- 482     :-        require(totalFee <= price, "Total amount of fees are more than the price");
+      482:+        require(totalFee <= price, "fees are more than the price");
  483, 483: 
  484, 484:         /* Amount that will be received by seller. */
  485, 485:         uint256 receiveAmount = price - totalFee;
```

##### - contracts/ExecutionDelegate.sol:[22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)

```diff
diff --git a/contracts/ExecutionDelegate.sol b/contracts/ExecutionDelegate.sol
index bb5e5ca..bf7d39e 100644
--- a/contracts/ExecutionDelegate.sol
+++ b/contracts/ExecutionDelegate.sol
@@ -19,7 +19,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   19,  19:     mapping(address => bool) public revokedApproval;
   20,  20: 
   21,  21:     modifier approvedContract() {
-  22     :-        require(contracts[msg.sender], "Contract is not approved to make transfers");
+       22:+        require(contracts[msg.sender], "Contract is not approved");
   23,  23:         _;
   24,  24:     }
   25,  25: 
diff --git a/tests/execution.test.ts b/tests/execution.test.ts
index 7b115b7..cc752e9 100644
--- a/tests/execution.test.ts
+++ b/tests/execution.test.ts
@@ -144,7 +144,7 @@ export function runExecuteTests(setupTest: any) {
  144, 144: 
  145, 145:       await expect(
  146, 146:         exchange.connect(bob).execute(sellInput, buyInput),
- 147     :-      ).to.be.revertedWith('Contract is not approved to make transfers');
+      147:+      ).to.be.revertedWith('Contract is not approved');
  148, 148:       await executionDelegate.approveContract(exchange.address);
  149, 149:     });
  150, 150:     it('should succeed is approval is given', async () => {
```

---

#### 6. **Using calldata instead of memory for read-only arguments in external functions saves gas (2 instances)**

Deployements. Gas Saved: **19 891**

Method Calls. Gas Saved: **0**

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 \* <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

##### - contracts/lib/MerkleVerifier.sol:[20](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L20), [35](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L35)

```diff
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..7ec9a5f 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -17,7 +17,7 @@ library MerkleVerifier {
   17,  17:     function _verifyProof(
   18,  18:         bytes32 leaf,
   19,  19:         bytes32 root,
-  20     :-        bytes32[] memory proof
+       20:+        bytes32[] calldata proof
   21,  21:     ) public pure {
   22,  22:         bytes32 computedRoot = _computeRoot(leaf, proof);
   23,  23:         if (computedRoot != root) {
@@ -32,7 +32,7 @@ library MerkleVerifier {
   32,  32:      */
   33,  33:     function _computeRoot(
   34,  34:         bytes32 leaf,
-  35     :-        bytes32[] memory proof
+       35:+        bytes32[] calldata proof
   36,  36:     ) public pure returns (bytes32) {
   37,  37:         bytes32 computedHash = leaf;
   38,  38:         for (uint256 i = 0; i < proof.length; i++) {
```

---

#### 7. **Using bools for storage incurs overhead (4 instances)**

Deployements. Gas Saved: **17 186**

Method Calls. Gas Saved: **777**

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

##### - contracts/BlurExchange.sol:[71](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L71)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..cc02f76 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -68,7 +68,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
   68,  68: 
   69,  69: 
   70,  70:     /* Storage */
-  71     :-    mapping(bytes32 => bool) public cancelledOrFilled;
+       71:+    mapping(bytes32 => uint256) public cancelledOrFilled;
   72,  72:     mapping(address => uint256) public nonces;
   73,  73: 
   74,  74: 
@@ -161,8 +161,8 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  161, 161:         );
  162, 162: 
  163, 163:         /* Mark orders as filled. */
- 164     :-        cancelledOrFilled[sellHash] = true;
- 165     :-        cancelledOrFilled[buyHash] = true;
+      164:+        cancelledOrFilled[sellHash] = 1;
+      165:+        cancelledOrFilled[buyHash] = 1;
  166, 166: 
  167, 167:         emit OrdersMatched(
  168, 168:             sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,
@@ -184,9 +184,9 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  184, 184: 
  185, 185:         bytes32 hash = _hashOrder(order, nonces[order.trader]);
  186, 186: 
- 187     :-        if (!cancelledOrFilled[hash]) {
+      187:+        if (0==cancelledOrFilled[hash]) {
  188, 188:             /* Mark order as cancelled, preventing it from being matched. */
- 189     :-            cancelledOrFilled[hash] = true;
+      189:+            cancelledOrFilled[hash] = 1;
  190, 190:             emit OrderCancelled(hash);
  191, 191:         }
  192, 192:     }
@@ -264,7 +264,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  264, 264:             /* Order must have a trader. */
  265, 265:             (order.trader != address(0)) &&
  266, 266:             /* Order must not be cancelled or filled. */
- 267     :-            (cancelledOrFilled[orderHash] == false) &&
+      267:+            (cancelledOrFilled[orderHash] == 0) &&
  268, 268:             /* Order must be settleable. */
  269, 269:             _canSettleOrder(order.listingTime, order.expirationTime)
  270, 270:         );
```

##### - contracts/ExecutionDelegate.sol:[18](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L18), [19](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L19)

```diff
diff --git a/contracts/ExecutionDelegate.sol b/contracts/ExecutionDelegate.sol
index bb5e5ca..7dd860d 100644
--- a/contracts/ExecutionDelegate.sol
+++ b/contracts/ExecutionDelegate.sol
@@ -15,11 +15,11 @@ import {IExecutionDelegate} from "./interfaces/IExecutionDelegate.sol";
   15,  15:  */
   16,  16: contract ExecutionDelegate is IExecutionDelegate, Ownable {
   17,  17: 
-  18     :-    mapping(address => bool) public contracts;
-  19     :-    mapping(address => bool) public revokedApproval;
+       18:+    mapping(address => uint256) public contracts;
+       19:+    mapping(address => uint256) public revokedApproval;
   20,  20: 
   21,  21:     modifier approvedContract() {
-  22     :-        require(contracts[msg.sender], "Contract is not approved to make transfers");
+       22:+        require(contracts[msg.sender]==1, "Contract is not approved to make transfers");
   23,  23:         _;
   24,  24:     }
   25,  25: 
@@ -34,7 +34,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   34,  34:      * @param _contract address of contract to approve
   35,  35:      */
   36,  36:     function approveContract(address _contract) onlyOwner external {
-  37     :-        contracts[_contract] = true;
+       37:+        contracts[_contract] = 1;
   38,  38:         emit ApproveContract(_contract);
   39,  39:     }
   40,  40: 
@@ -43,7 +43,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   43,  43:      * @param _contract address of contract to revoke approval
   44,  44:      */
   45,  45:     function denyContract(address _contract) onlyOwner external {
-  46     :-        contracts[_contract] = false;
+       46:+        contracts[_contract] = 0;
   47,  47:         emit DenyContract(_contract);
   48,  48:     }
   49,  49: 
@@ -51,7 +51,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   51,  51:      * @dev Block contract from making transfers on-behalf of a specific user
   52,  52:      */
   53,  53:     function revokeApproval() external {
-  54     :-        revokedApproval[msg.sender] = true;
+       54:+        revokedApproval[msg.sender] = 1;
   55,  55:         emit RevokeApproval(msg.sender);
   56,  56:     }
   57,  57: 
@@ -59,7 +59,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   59,  59:      * @dev Allow contract to make transfers on-behalf of a specific user
   60,  60:      */
   61,  61:     function grantApproval() external {
-  62     :-        revokedApproval[msg.sender] = false;
+       62:+        revokedApproval[msg.sender] = 0;
   63,  63:         emit GrantApproval(msg.sender);
   64,  64:     }
   65,  65: 
@@ -74,7 +74,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   74,  74:         approvedContract
   75,  75:         external
   76,  76:     {
-  77     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       77:+        require(revokedApproval[from] == 0, "User has revoked approval");
   78,  78:         IERC721(collection).transferFrom(from, to, tokenId);
   79,  79:     }
   80,  80: 
@@ -89,7 +89,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   89,  89:         approvedContract
   90,  90:         external
   91,  91:     {
-  92     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       92:+        require(revokedApproval[from] == 0, "User has revoked approval");
   93,  93:         IERC721(collection).safeTransferFrom(from, to, tokenId);
   94,  94:     }
   95,  95: 
@@ -105,7 +105,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  105, 105:         approvedContract
  106, 106:         external
  107, 107:     {
- 108     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      108:+        require(revokedApproval[from] == 0, "User has revoked approval");
  109, 109:         IERC1155(collection).safeTransferFrom(from, to, tokenId, amount, "");
  110, 110:     }
  111, 111: 
@@ -121,7 +121,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  121, 121:         external
  122, 122:         returns (bool)
  123, 123:     {
- 124     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      124:+        require(revokedApproval[from] == 0, "User has revoked approval");
  125, 125:         return IERC20(token).transferFrom(from, to, amount);
  126, 126:     }
  127, 127: }
```

##### - contracts/lib/ReentrancyGuarded.sol:[10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)

```diff
diff --git a/contracts/lib/ReentrancyGuarded.sol b/contracts/lib/ReentrancyGuarded.sol
index 8028889..ad68288 100644
--- a/contracts/lib/ReentrancyGuarded.sol
+++ b/contracts/lib/ReentrancyGuarded.sol
@@ -7,14 +7,14 @@ pragma solidity 0.8.13;
    7,   7:  */
    8,   8: contract ReentrancyGuarded {
    9,   9: 
-  10     :-    bool reentrancyLock = false;
+       10:+    uint256 reentrancyLock = 0;
   11,  11: 
   12,  12:     /* Prevent a contract function from being reentrant-called. */
   13,  13:     modifier reentrancyGuard {
-  14     :-        require(!reentrancyLock, "Reentrancy detected");
-  15     :-        reentrancyLock = true;
+       14:+        require(reentrancyLock == 0, "Reentrancy detected");
+       15:+        reentrancyLock = 1;
   16,  16:         _;
-  17     :-        reentrancyLock = false;
+       17:+        reentrancyLock = 0;
   18,  18:     }
   19,  19: 
   20,  20: }
```

---

#### 8. **Unnecessary if statement (3 instances)**

Deployements. Gas Saved: **16 191**

Method Calls. Gas Saved: **4**

Note: Enum variable can take only defined values

##### - contracts/BlurExchange.sol:[357](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L357), [386](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L386), [539](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L539)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..dad0888 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -354,7 +354,8 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  354, 354:         if (signatureVersion == SignatureVersion.Single) {
  355, 355:             /* Single-listing authentication: Order signed by trader */
  356, 356:             hashToSign = _hashToSign(orderHash);
- 357     :-        } else if (signatureVersion == SignatureVersion.Bulk) {
+      357:+        } 
+      358:+        else {
  358, 359:             /* Bulk-listing authentication: Merkle root of orders signed by trader */
  359, 360:             (bytes32[] memory merklePath) = abi.decode(extraSignature, (bytes32[]));
  360, 361: 
@@ -383,7 +384,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  383, 384:         uint8 v; bytes32 r; bytes32 s;
  384, 385:         if (signatureVersion == SignatureVersion.Single) {
  385, 386:             (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
- 386     :-        } else if (signatureVersion == SignatureVersion.Bulk) {
+      387:+        } else {
  387, 388:             /* If the signature was a bulk listing the merkle path musted be unpacked before the oracle signature. */
  388, 389:             (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
  389, 390:             v = _v; r = _r; s = _s;
@@ -536,7 +537,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  536, 537:         /* Call execution delegate. */
  537, 538:         if (assetType == AssetType.ERC721) {
  538, 539:             executionDelegate.transferERC721(collection, from, to, tokenId);
- 539     :-        } else if (assetType == AssetType.ERC1155) {
+      540:+        } else {
  540, 541:             executionDelegate.transferERC1155(collection, from, to, tokenId, amount);
  541, 542:         }
  542, 543:     }
```

---

#### 9. **`<x> = <x> + 1` even more efficient than increment. (5 instances)**

Deployements. Gas Saved: **9 654**

Method Calls. Gas Saved: **-50**

##### - contracts/BlurExchange.sol:[199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199), [476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..d866e5e 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -196,7 +196,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  196, 196:      * @param orders Orders to cancel
  197, 197:      */
  198, 198:     function cancelOrders(Order[] calldata orders) external {
- 199     :-        for (uint8 i = 0; i < orders.length; i++) {
+      199:+        for (uint8 i = 0; i < orders.length; i = i + 1) {
  200, 200:             cancelOrder(orders[i]);
  201, 201:         }
  202, 202:     }
@@ -473,7 +473,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  473, 473:         uint256 price
  474, 474:     ) internal returns (uint256) {
  475, 475:         uint256 totalFee = 0;
- 476     :-        for (uint8 i = 0; i < fees.length; i++) {
+      476:+        for (uint8 i = 0; i < fees.length; i = i + 1) {
  477, 477:             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
  478, 478:             _transferTo(paymentToken, from, fees[i].recipient, fee);
  479, 479:             totalFee += fee;
```

##### - contracts/PolicyManager.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..abfe2b5 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -74,7 +74,7 @@ contract PolicyManager is IPolicyManager, Ownable {
   74,  74: 
   75,  75:         address[] memory whitelistedPolicies = new address[](length);
   76,  76: 
-  77     :-        for (uint256 i = 0; i < length; i++) {
+       77:+        for (uint256 i = 0; i < length; i = i + 1) {
   78,  78:             whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
   79,  79:         }
   80,  80: 
```

##### - contracts/lib/EIP712.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

```diff
diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..15a6190 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -74,7 +74,7 @@ contract EIP712 {
   74,  74:         bytes32[] memory feeHashes = new bytes32[](
   75,  75:             fees.length
   76,  76:         );
-  77     :-        for (uint256 i = 0; i < fees.length; i++) {
+       77:+        for (uint256 i = 0; i < fees.length; i = i + 1) {
   78,  78:             feeHashes[i] = _hashFee(fees[i]);
   79,  79:         }
   80,  80:         return keccak256(abi.encodePacked(feeHashes));
```

##### -contracts/lib/MerkleVerifier.sol:[38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

```diff
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..fe19357 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -35,7 +35,7 @@ library MerkleVerifier {
   35,  35:         bytes32[] memory proof
   36,  36:     ) public pure returns (bytes32) {
   37,  37:         bytes32 computedHash = leaf;
-  38     :-        for (uint256 i = 0; i < proof.length; i++) {
+       38:+        for (uint256 i = 0; i < proof.length; i = i + 1) {
   39,  39:             bytes32 proofElement = proof[i];
   40,  40:             computedHash = _hashPair(computedHash, proofElement);
   41,  41:         }
```

---

### 10. **`private` functions only called once can be inlined to save gas (1 instance)**

Deployements. Gas Saved: **4 071**

Method Calls. Gas Saved: **0**

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.    

##### - contracts/lib/MerkleVerifier.sol:[45-48](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L45-L47)

```diff
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..4559f23 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -37,15 +37,13 @@ library MerkleVerifier {
   37,  37:         bytes32 computedHash = leaf;
   38,  38:         for (uint256 i = 0; i < proof.length; i++) {
   39,  39:             bytes32 proofElement = proof[i];
-  40     :-            computedHash = _hashPair(computedHash, proofElement);
+       40:+            computedHash = computedHash < proofElement ? 
+       41:+                _efficientHash(computedHash, proofElement) : 
+       42:+                _efficientHash(proofElement, computedHash);
   41,  43:         }
   42,  44:         return computedHash;
   43,  45:     }
   44,  46: 
-  45     :-    function _hashPair(bytes32 a, bytes32 b) private pure returns (bytes32) {
-  46     :-        return a < b ? _efficientHash(a, b) : _efficientHash(b, a);
-  47     :-    }
-  48     :-
   49,  47:     function _efficientHash(
   50,  48:         bytes32 a,
   51,  49:         bytes32 b
```

---

#### 11. **Unchecking arithmetics operations that can't underflow/overflow (2 instances)**

Deployements. Gas Saved: **3 894**

Method Calls. Gas Saved: **65**

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic

##### - contracts/BlurExchange.sol:[479](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L479)

NOTE: Cannot overflow because `fee = X / 10000` and loop limited by `uint8.MAX`

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..9bec52d 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -476,7 +476,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  476, 476:         for (uint8 i = 0; i < fees.length; i++) {
  477, 477:             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
  478, 478:             _transferTo(paymentToken, from, fees[i].recipient, fee);
- 479     :-            totalFee += fee;
+      479:+            unchecked { totalFee += fee; }
  480, 480:         }
  481, 481: 
  482, 482:         require(totalFee <= price, "Total amount of fees are more than the price");
```

##### - contracts/PolicyManager.sol:[72](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L72)

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..49d457a 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -69,7 +69,7 @@ contract PolicyManager is IPolicyManager, Ownable {
   69,  69:         uint256 length = size;
   70,  70: 
   71,  71:         if (length > _whitelistedPolicies.length() - cursor) {
-  72     :-            length = _whitelistedPolicies.length() - cursor;
+       72:+            unchecked { length = _whitelistedPolicies.length() - cursor; }
   73,  73:         }
   74,  74: 
   75,  75:         address[] memory whitelistedPolicies = new address[](length);
```

---

#### 12. **Do not emit storage variables, when stack available (5 instances)**

Deployements. Gas Saved: **2 988**

Method Calls. Gas Saved: **144**

#### - contracts/BlurExchange.sol:[208-209](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208-L209), [221](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L221), [230](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L230), [239](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L239), [247](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L247)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..fbedbc9 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -205,8 +205,8 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  205, 205:      * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
  206, 206:      */
  207, 207:     function incrementNonce() external {
- 208     :-        nonces[msg.sender] += 1;
- 209     :-        emit NonceIncremented(msg.sender, nonces[msg.sender]);
+      208:+        uint256 _nonce = nonces[msg.sender] += 1;
+      209:+        emit NonceIncremented(msg.sender, _nonce);
  210, 210:     }
  211, 211: 
  212, 212: 
@@ -218,7 +218,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  218, 218:     {
  219, 219:         require(address(_executionDelegate) != address(0), "Address cannot be zero");
  220, 220:         executionDelegate = _executionDelegate;
- 221     :-        emit NewExecutionDelegate(executionDelegate);
+      221:+        emit NewExecutionDelegate(_executionDelegate);
  222, 222:     }
  223, 223: 
  224, 224:     function setPolicyManager(IPolicyManager _policyManager)
@@ -227,7 +227,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  227, 227:     {
  228, 228:         require(address(_policyManager) != address(0), "Address cannot be zero");
  229, 229:         policyManager = _policyManager;
- 230     :-        emit NewPolicyManager(policyManager);
+      230:+        emit NewPolicyManager(_policyManager);
  231, 231:     }
  232, 232: 
  233, 233:     function setOracle(address _oracle)
@@ -236,7 +236,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  236, 236:     {
  237, 237:         require(_oracle != address(0), "Address cannot be zero");
  238, 238:         oracle = _oracle;
- 239     :-        emit NewOracle(oracle);
+      239:+        emit NewOracle(_oracle);
  240, 240:     }
  241, 241: 
  242, 242:     function setBlockRange(uint256 _blockRange)
@@ -244,7 +244,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  244, 244:         onlyOwner
  245, 245:     {
  246, 246:         blockRange = _blockRange;
- 247     :-        emit NewBlockRange(blockRange);
+      247:+        emit NewBlockRange(_blockRange);
  248, 248:     }
  249, 249: 
  250, 250: 
```

---

#### 13. **`++i` costs less gas than `i++`, especially when it's used in for-loops (`--i`/`i--` too) (4 instances)**

Deployements. Gas Saved: **2 136**

Method Calls. Gas Saved: **40**

```
Saves 6 gas per loop

Pre-increments and pre-decrements are cheaper.

For a uint256 i variable, the following is true with the Optimizer enabled at 10k:

Increment:

i += 1 is the most expensive form
i++ costs 6 gas less than i += 1
++i costs 5 gas less than i++ (11 gas less than i += 1)
Decrement:

i -= 1 is the most expensive form
i-- costs 11 gas less than i -= 1
--i costs 5 gas less than i-- (16 gas less than i -= 1)
```


##### - contracts/BlurExchange.sol:[199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199), [476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..e4ebe97 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -196,7 +196,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  196, 196:      * @param orders Orders to cancel
  197, 197:      */
  198, 198:     function cancelOrders(Order[] calldata orders) external {
- 199     :-        for (uint8 i = 0; i < orders.length; i++) {
+      199:+        for (uint8 i = 0; i < orders.length; ++i) {
  200, 200:             cancelOrder(orders[i]);
  201, 201:         }
  202, 202:     }
@@ -473,7 +473,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  473, 473:         uint256 price
  474, 474:     ) internal returns (uint256) {
  475, 475:         uint256 totalFee = 0;
- 476     :-        for (uint8 i = 0; i < fees.length; i++) {
+      476:+        for (uint8 i = 0; i < fees.length; ++i) {
  477, 477:             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
  478, 478:             _transferTo(paymentToken, from, fees[i].recipient, fee);
  479, 479:             totalFee += fee;
```

##### - contracts/PolicyManager.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..fba03c4 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -74,7 +74,7 @@ contract PolicyManager is IPolicyManager, Ownable {
   74,  74: 
   75,  75:         address[] memory whitelistedPolicies = new address[](length);
   76,  76: 
-  77     :-        for (uint256 i = 0; i < length; i++) {
+       77:+        for (uint256 i = 0; i < length; ++i) {
   78,  78:             whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
   79,  79:         }
   80,  80: 
```

##### - contracts/lib/EIP712.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

```diff
diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..24aca36 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -74,7 +74,7 @@ contract EIP712 {
   74,  74:         bytes32[] memory feeHashes = new bytes32[](
   75,  75:             fees.length
   76,  76:         );
-  77     :-        for (uint256 i = 0; i < fees.length; i++) {
+       77:+        for (uint256 i = 0; i < fees.length; ++i) {
   78,  78:             feeHashes[i] = _hashFee(fees[i]);
   79,  79:         }
   80,  80:         return keccak256(abi.encodePacked(feeHashes));
```

---

#### 14. **State variables should be cached in stack variables rather than re-reading them from storage (1 instance)**

Deployements. Gas Saved: **1 734**

Method Calls. Gas Saved: **0**

The code can be optimized by minimising the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

#### - contracts/PolicyManager.sol:[71-72](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L71-L72)

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..01ced6e 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -67,9 +67,10 @@ contract PolicyManager is IPolicyManager, Ownable {
   67,  67:         returns (address[] memory, uint256)
   68,  68:     {
   69,  69:         uint256 length = size;
+       70:+        uint256 WPlength = _whitelistedPolicies.length();
   70,  71: 
-  71     :-        if (length > _whitelistedPolicies.length() - cursor) {
-  72     :-            length = _whitelistedPolicies.length() - cursor;
+       72:+        if (length > WPlength - cursor) {
+       73:+            length = WPlength - cursor;
   73,  74:         }
   74,  75: 
   75,  76:         address[] memory whitelistedPolicies = new address[](length);
```

---

#### General optimizations:

---

#### 1. **`<array>.length` should not be looked up in every loop of a for-loop (4 instances)**

NOTE: Estimation highly depend on amount of input elements

The overheads outlined below are PER LOOP, excluding the first loop

`storage` arrays incur a Gwarmaccess (100 gas)
`memory` arrays use MLOAD (3 gas)
`calldata` arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

##### - contracts/BlurExchange.sol:[199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199), [476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..7bb0d1d 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -196,7 +196,8 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  196, 196:      * @param orders Orders to cancel
  197, 197:      */
  198, 198:     function cancelOrders(Order[] calldata orders) external {
- 199     :-        for (uint8 i = 0; i < orders.length; i++) {
+      199:+        uint256 orderLength = orders.length;
+      200:+        for (uint8 i = 0; i < orderLength; i++) {
  200, 201:             cancelOrder(orders[i]);
  201, 202:         }
  202, 203:     }
@@ -473,7 +474,8 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  473, 474:         uint256 price
  474, 475:     ) internal returns (uint256) {
  475, 476:         uint256 totalFee = 0;
- 476     :-        for (uint8 i = 0; i < fees.length; i++) {
+      477:+        uint256 length = fees.length;
+      478:+        for (uint8 i = 0; i < length; i++) {
  477, 479:             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
  478, 480:             _transferTo(paymentToken, from, fees[i].recipient, fee);
  479, 481:             totalFee += fee;
```

##### - contracts/lib/EIP712.sol:[74-77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L74-L77)

```diff
diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..0e8f709 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -71,10 +71,10 @@ contract EIP712 {
   71,  71:         pure
   72,  72:         returns (bytes32)
   73,  73:     {
-  74     :-        bytes32[] memory feeHashes = new bytes32[](
-  75     :-            fees.length
-  76     :-        );
-  77     :-        for (uint256 i = 0; i < fees.length; i++) {
+       74:+        uint256 feeLength = fees.length;
+       75:+        bytes32[] memory feeHashes = new bytes32[](feeLength);
+       76:+        
+       77:+        for (uint256 i = 0; i < feeLength; i++) {
   78,  78:             feeHashes[i] = _hashFee(fees[i]);
   79,  79:         }
   80,  80:         return keccak256(abi.encodePacked(feeHashes));
```

##### - contracts/lib/MerkleVerifier.sol:[38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

```diff
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..a2caacb 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -35,7 +35,8 @@ library MerkleVerifier {
   35,  35:         bytes32[] memory proof
   36,  36:     ) public pure returns (bytes32) {
   37,  37:         bytes32 computedHash = leaf;
-  38     :-        for (uint256 i = 0; i < proof.length; i++) {
+       38:+        uint256 proofLength = proof.length;
+       39:+        for (uint256 i = 0; i < proofLength; i++) {
   39,  40:             bytes32 proofElement = proof[i];
   40,  41:             computedHash = _hashPair(computedHash, proofElement);
   41,  42:         }
```

---

#### 2. **It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied (5 instances)**

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address...). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

##### - contracts/BlurExchange.sol:[199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199), [475](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..a7fae13 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -196,7 +196,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  196, 196:      * @param orders Orders to cancel
  197, 197:      */
  198, 198:     function cancelOrders(Order[] calldata orders) external {
- 199     :-        for (uint8 i = 0; i < orders.length; i++) {
+      199:+        for (uint8 i; i < orders.length; i++) {
  200, 200:             cancelOrder(orders[i]);
  201, 201:         }
  202, 202:     }
@@ -472,8 +472,8 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  472, 472:         address from,
  473, 473:         uint256 price
  474, 474:     ) internal returns (uint256) {
- 475     :-        uint256 totalFee = 0;
- 476     :-        for (uint8 i = 0; i < fees.length; i++) {
+      475:+        uint256 totalFee;
+      476:+        for (uint8 i; i < fees.length; i++) {
  477, 477:             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
  478, 478:             _transferTo(paymentToken, from, fees[i].recipient, fee);
  479, 479:             totalFee += fee;
```

##### - contracts/PolicyManager.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..d5c4be8 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -74,7 +74,7 @@ contract PolicyManager is IPolicyManager, Ownable {
   74,  74: 
   75,  75:         address[] memory whitelistedPolicies = new address[](length);
   76,  76: 
-  77     :-        for (uint256 i = 0; i < length; i++) {
+       77:+        for (uint256 i; i < length; i++) {
   78,  78:             whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
   79,  79:         }
   80,  80: 
```

##### - contracts/lib/EIP712.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

```diff
diff --git a/contracts/lib/EIP712.sol b/contracts/lib/EIP712.sol
index 0ee537d..4b1c94c 100644
--- a/contracts/lib/EIP712.sol
+++ b/contracts/lib/EIP712.sol
@@ -74,7 +74,7 @@ contract EIP712 {
   74,  74:         bytes32[] memory feeHashes = new bytes32[](
   75,  75:             fees.length
   76,  76:         );
-  77     :-        for (uint256 i = 0; i < fees.length; i++) {
+       77:+        for (uint256 i; i < fees.length; i++) {
   78,  78:             feeHashes[i] = _hashFee(fees[i]);
   79,  79:         }
   80,  80:         return keccak256(abi.encodePacked(feeHashes));
```

##### - contracts/lib/MerkleVerifier.sol:[38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

```diff
diff --git a/contracts/lib/MerkleVerifier.sol b/contracts/lib/MerkleVerifier.sol
index 8ca0659..333e816 100644
--- a/contracts/lib/MerkleVerifier.sol
+++ b/contracts/lib/MerkleVerifier.sol
@@ -35,7 +35,7 @@ library MerkleVerifier {
   35,  35:         bytes32[] memory proof
   36,  36:     ) public pure returns (bytes32) {
   37,  37:         bytes32 computedHash = leaf;
-  38     :-        for (uint256 i = 0; i < proof.length; i++) {
+       38:+        for (uint256 i; i < proof.length; i++) {
   39,  39:             bytes32 proofElement = proof[i];
   40,  40:             computedHash = _hashPair(computedHash, proofElement);
   41,  41:         }
```

---

#### 3. **Don't compare boolean expressions to boolean literals (5 instances)**

if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

##### - contracts/BlurExchange.sol:[267](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L267)

```diff
diff --git a/contracts/BlurExchange.sol b/contracts/BlurExchange.sol
index 6fdc510..f8c619b 100644
--- a/contracts/BlurExchange.sol
+++ b/contracts/BlurExchange.sol
@@ -264,7 +264,7 @@ contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgrad
  264, 264:             /* Order must have a trader. */
  265, 265:             (order.trader != address(0)) &&
  266, 266:             /* Order must not be cancelled or filled. */
- 267     :-            (cancelledOrFilled[orderHash] == false) &&
+      267:+            (!cancelledOrFilled[orderHash]) &&
  268, 268:             /* Order must be settleable. */
  269, 269:             _canSettleOrder(order.listingTime, order.expirationTime)
  270, 270:         );
```

##### - contracts/ExecutionDelegate.sol:[77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77), [92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92), [108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108), [124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)

```diff
diff --git a/contracts/ExecutionDelegate.sol b/contracts/ExecutionDelegate.sol
index bb5e5ca..f8b1dcc 100644
--- a/contracts/ExecutionDelegate.sol
+++ b/contracts/ExecutionDelegate.sol
@@ -74,7 +74,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   74,  74:         approvedContract
   75,  75:         external
   76,  76:     {
-  77     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       77:+        require(!revokedApproval[from], "User has revoked approval");
   78,  78:         IERC721(collection).transferFrom(from, to, tokenId);
   79,  79:     }
   80,  80: 
@@ -89,7 +89,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
   89,  89:         approvedContract
   90,  90:         external
   91,  91:     {
-  92     :-        require(revokedApproval[from] == false, "User has revoked approval");
+       92:+        require(!revokedApproval[from], "User has revoked approval");
   93,  93:         IERC721(collection).safeTransferFrom(from, to, tokenId);
   94,  94:     }
   95,  95: 
@@ -105,7 +105,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  105, 105:         approvedContract
  106, 106:         external
  107, 107:     {
- 108     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      108:+        require(!revokedApproval[from], "User has revoked approval");
  109, 109:         IERC1155(collection).safeTransferFrom(from, to, tokenId, amount, "");
  110, 110:     }
  111, 111: 
@@ -121,7 +121,7 @@ contract ExecutionDelegate is IExecutionDelegate, Ownable {
  121, 121:         external
  122, 122:         returns (bool)
  123, 123:     {
- 124     :-        require(revokedApproval[from] == false, "User has revoked approval");
+      124:+        require(!revokedApproval[from], "User has revoked approval");
  125, 125:         return IERC20(token).transferFrom(from, to, amount);
  126, 126:     }
  127, 127: }
```

---

#### 99. **Overall Gas**

The amount of gas saved after merge all optimizations

Deployements. Gas Saved: **519 061**
Method Calls. Gas Saved: **1 422**

```diff
diff --git a/origin.txt b/total.txt
index 6703ecc..15fd417 100644
--- a/origin.txt
+++ b/total.txt
@@ -194,33 +194,33 @@ Deployed: ERC1967Proxy to: 0xC9a43158891282A2B1475592D5719c001986Aaec
 ······················|························|·············|·············|·············|···············|··············
 |  Contract           ·  Method                ·  Min        ·  Max        ·  Avg        ·  # calls      ·  eur (avg)  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  cancelOrder           ·      61089  ·      61113  ·      61099  ·            5  ·          -  │
+|  BlurExchange       ·  cancelOrder           ·      60925  ·      60949  ·      60935  ·            5  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  cancelOrders          ·          -  ·          -  ·      71598  ·            1  ·          -  │
+|  BlurExchange       ·  cancelOrders          ·          -  ·          -  ·      71177  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  close                 ·          -  ·          -  ·      29341  ·            2  ·          -  │
+|  BlurExchange       ·  close                 ·          -  ·          -  ·      29297  ·            2  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  execute               ·     244937  ·     282894  ·     272762  ·           20  ·          -  │
+|  BlurExchange       ·  execute               ·     244160  ·     282252  ·     272055  ·           20  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  incrementNonce        ·      32829  ·      49929  ·      41379  ·            4  ·          -  │
+|  BlurExchange       ·  incrementNonce        ·      32708  ·      49808  ·      41258  ·            4  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
 |  BlurExchange       ·  open                  ·          -  ·          -  ·      51236  ·            2  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  setBlockRange         ·          -  ·          -  ·      31841  ·            1  ·          -  │
+|  BlurExchange       ·  setBlockRange         ·          -  ·          -  ·      31797  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  setExecutionDelegate  ·          -  ·          -  ·      34980  ·            1  ·          -  │
+|  BlurExchange       ·  setExecutionDelegate  ·          -  ·          -  ·      35009  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  setOracle             ·          -  ·          -  ·      32190  ·            1  ·          -  │
+|  BlurExchange       ·  setOracle             ·          -  ·          -  ·      32262  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  BlurExchange       ·  setPolicyManager      ·          -  ·          -  ·      35011  ·            1  ·          -  │
+|  BlurExchange       ·  setPolicyManager      ·          -  ·          -  ·      35040  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  ExecutionDelegate  ·  approveContract       ·      27351  ·      47263  ·      44197  ·           13  ·          -  │
+|  ExecutionDelegate  ·  approveContract       ·      27333  ·      47245  ·      44179  ·           13  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  ExecutionDelegate  ·  denyContract          ·          -  ·          -  ·      25331  ·            1  ·          -  │
+|  ExecutionDelegate  ·  denyContract          ·          -  ·          -  ·      25322  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  ExecutionDelegate  ·  grantApproval         ·          -  ·          -  ·      22701  ·            1  ·          -  │
+|  ExecutionDelegate  ·  grantApproval         ·          -  ·          -  ·      22692  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
-|  ExecutionDelegate  ·  revokeApproval        ·          -  ·          -  ·      44585  ·            1  ·          -  │
+|  ExecutionDelegate  ·  revokeApproval        ·          -  ·          -  ·      44570  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
 |  MockERC1155        ·  mint                  ·          -  ·          -  ·      50311  ·            1  ·          -  │
 ······················|························|·············|·············|·············|···············|··············
@@ -240,11 +240,11 @@ Deployed: ERC1967Proxy to: 0xC9a43158891282A2B1475592D5719c001986Aaec
 ······················|························|·············|·············|·············|···············|··············
 |  Deployments                                 ·                                         ·  % of limit   ·             │
 ···············································|·············|·············|·············|···············|··············
-|  ERC1967Proxy                                ·     456147  ·     456159  ·     456154  ·        1.5 %  ·          -  │
+|  ERC1967Proxy                                ·     456097  ·     456109  ·     456104  ·        1.5 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  ExecutionDelegate                           ·          -  ·          -  ·     722913  ·        2.4 %  ·          -  │
+|  ExecutionDelegate                           ·          -  ·          -  ·     590972  ·          2 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  MerkleVerifier                              ·          -  ·          -  ·     207707  ·        0.7 %  ·          -  │
+|  MerkleVerifier                              ·          -  ·          -  ·     172972  ·        0.6 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
 |  MockERC1155                                 ·          -  ·          -  ·    1287793  ·        4.3 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
@@ -252,15 +252,15 @@ Deployed: ERC1967Proxy to: 0xC9a43158891282A2B1475592D5719c001986Aaec
 ···············································|·············|·············|·············|···············|··············
 |  MockERC721                                  ·          -  ·          -  ·    1493106  ·          5 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  PolicyManager                               ·          -  ·          -  ·     582410  ·        1.9 %  ·          -  │
+|  PolicyManager                               ·          -  ·          -  ·     551659  ·        1.8 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
 |  StandardPolicyERC1155                       ·          -  ·          -  ·     212360  ·        0.7 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
 |  StandardPolicyERC721                        ·          -  ·          -  ·     212564  ·        0.7 %  ·          -  │
 ···············································|·············|·············|·············|···············|··············
-|  TestBlurExchange                            ·          -  ·          -  ·    3208152  ·       10.7 %  ·          -  │
+|  TestBlurExchange                            ·          -  ·          -  ·    2886568  ·        9.6 %  ·          -  │
 ·----------------------------------------------|-------------|-------------|-------------|---------------|-------------·
 
-  93 passing (17s)
+  93 passing (16s)
 
-Done in 24.96s.
+Done in 24.07s.
```
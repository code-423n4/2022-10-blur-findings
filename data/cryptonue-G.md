# ARRAY LENGTH SHOULD NOT BE LOOKED UP IN EVERY ITERATION

It wastes gas to read an array’s length in every iteration of a for loop, even if it is a memory or calldata array: 3 gas per read.

The instances are:
```
File: BlurExchange.sol
198:     function cancelOrders(Order[] calldata orders) external {
199:         for (uint8 i = 0; i < orders.length; i++) {
200:             cancelOrder(orders[i]);
201:         }
202:     }


File: BlurExchange.sol
476:         for (uint8 i = 0; i < fees.length; i++) {
477:             uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
478:             _transferTo(paymentToken, from, fees[i].recipient, fee);
479:             totalFee += fee;
480:         }

File: EIP712.sol
77:         for (uint256 i = 0; i < fees.length; i++) {
78:             feeHashes[i] = _hashFee(fees[i]);
79:         }

File: MerkleVerifier.sol
38:         for (uint256 i = 0; i < proof.length; i++) {
39:             bytes32 proofElement = proof[i];
40:             computedHash = _hashPair(computedHash, proofElement);
41:         }
```

## Recommended Mitigation

Caching the length in a variable before the for loop.


# CACHING STORAGE VARIABLES IN LOCAL VARIABLES TO SAVE GAS

Anytime you are reading from storage more than once, it is cheaper in gas cost to cache the variable: a SLOAD cost 100gas, while MLOAD and MSTORE cost 3 gas.

In particular, in `for` loops, when using the length of a storage array as the condition being checked after each loop, caching the array length can yield significant gas savings if the array length is high

Proof of concept:

`executionDelegate` is being read twice.

```
File: BlurExchange.sol
525:     function _executeTokenTransfer(
526:         address collection,
527:         address from,
528:         address to,
529:         uint256 tokenId,
530:         uint256 amount,
531:         AssetType assetType
532:     ) internal {
533:         /* Assert collection exists. */
534:         require(_exists(collection), "Collection does not exist");
535: 
536:         /* Call execution delegate. */
537:         if (assetType == AssetType.ERC721) {
538:             executionDelegate.transferERC721(collection, from, to, tokenId);
539:         } else if (assetType == AssetType.ERC1155) {
540:             executionDelegate.transferERC1155(collection, from, to, tokenId, amount);
541:         }
542:     }
```

`policyManager` being used twice:
```
File: BlurExchange.sol
416:     function _canMatchOrders(Order calldata sell, Order calldata buy)
417:         internal
418:         view
419:         returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
420:     {
421:         bool canMatch;
422:         if (sell.listingTime <= buy.listingTime) {
423:             /* Seller is maker. */
424:             require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
425:             (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy);
426:         } else {
427:             /* Buyer is maker. */
428:             require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
429:             (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell);
430:         }
431:         require(canMatch, "Orders cannot be matched");
432: 
433:         return (price, tokenId, amount, assetType);
434:     }
```

# CUSTOM ERRORS

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained (here)[https://blog.soliditylang.org/2021/04/21/custom-errors/]

```
File: BlurExchange.sol
139:         require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140:         require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
141: 
142:         require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143:         require(_validateSignatures(buy, buyHash), "Buy failed authorization");

File: BlurExchange.sol
219:         require(address(_executionDelegate) != address(0), "Address cannot be zero");

File: BlurExchange.sol
228:         require(address(_policyManager) != address(0), "Address cannot be zero");

File: BlurExchange.sol
237:         require(_oracle != address(0), "Address cannot be zero");

File: BlurExchange.sol
318:             require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

File: BlurExchange.sol
407:         require(v == 27 || v == 28, "Invalid v parameter");

File: BlurExchange.sol
424:             require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

File: BlurExchange.sol
431:         require(canMatch, "Orders cannot be matched");

File: BlurExchange.sol
482:         require(totalFee <= price, "Total amount of fees are more than the price");

File: BlurExchange.sol
534:         require(_exists(collection), "Collection does not exist");
```

# MATHEMATICAL OPTIMIZATIONS

X += Y costs 22 more gas than X = X + Y. This can mean a lot of gas wasted in a function call when the computation is repeated n times (loops)

Proof of Concept

```
File: BlurExchange.sol
208:         nonces[msg.sender] += 1;

File: BlurExchange.sol
479:             totalFee += fee;
```

# SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

If the project using the Optimizer at 200, instead of using the `&&` operator in a single require statement to check multiple conditions, Consider using multiple require statements with 1 condition per require statement:

```
File: BlurExchange.sol
263:         return (
264:             /* Order must have a trader. */
265:             (order.trader != address(0)) &&
266:             /* Order must not be cancelled or filled. */
267:             (cancelledOrFilled[orderHash] == false) &&
268:             /* Order must be settleable. */
269:             _canSettleOrder(order.listingTime, order.expirationTime)
270:         );


File: BlurExchange.sol
283:         return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);


File: StandardPolicyERC721.sol
24:         return (
25:             (makerAsk.side != takerBid.side) &&
26:             (makerAsk.paymentToken == takerBid.paymentToken) &&
27:             (makerAsk.collection == takerBid.collection) &&
28:             (makerAsk.tokenId == takerBid.tokenId) &&
29:             (makerAsk.matchingPolicy == takerBid.matchingPolicy) &&
30:             (makerAsk.price == takerBid.price),
31:             makerAsk.price,
32:             makerAsk.tokenId,
33:             1,
34:             AssetType.ERC721
35:         );


File: StandardPolicyERC721.sol
50:         return (
51:             (makerBid.side != takerAsk.side) &&
52:             (makerBid.paymentToken == takerAsk.paymentToken) &&
53:             (makerBid.collection == takerAsk.collection) &&
54:             (makerBid.tokenId == takerAsk.tokenId) &&
55:             (makerBid.matchingPolicy == takerAsk.matchingPolicy) &&
56:             (makerBid.price == takerAsk.price),
57:             makerBid.price,
58:             makerBid.tokenId,
59:             1,
60:             AssetType.ERC721
61:         );


File: StandardPolicyERC1155.sol
24:         return (
25:             (makerAsk.side != takerBid.side) &&
26:             (makerAsk.paymentToken == takerBid.paymentToken) &&
27:             (makerAsk.collection == takerBid.collection) &&
28:             (makerAsk.tokenId == takerBid.tokenId) &&
29:             (makerAsk.matchingPolicy == takerBid.matchingPolicy) &&
30:             (makerAsk.price == takerBid.price),
31:             makerAsk.price,
32:             makerAsk.tokenId,
33:             1,
34:             AssetType.ERC1155
35:         );


File: StandardPolicyERC1155.sol
50:         return (
51:             (makerBid.side != takerAsk.side) &&
52:             (makerBid.paymentToken == takerAsk.paymentToken) &&
53:             (makerBid.collection == takerAsk.collection) &&
54:             (makerBid.tokenId == takerAsk.tokenId) &&
55:             (makerBid.matchingPolicy == takerAsk.matchingPolicy) &&
56:             (makerBid.price == takerAsk.price),
57:             makerBid.price,
58:             makerBid.tokenId,
59:             1,
60:             AssetType.ERC1155
61:         );
```

Please, note that this might not hold true at a higher number of runs for the Optimizer (10k). However, it indeed is true at 200.

# ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 (SAME FOR --I VS I-- OR I -= 1)

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

*   `i += 1` is the most expensive form
*   `i++` costs 6 gas less than `i += 1`
*   `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

*   `i -= 1` is the most expensive form
*   `i--` costs 11 gas less than `i -= 1`
*   `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```

However, pre-increments (or pre-decrements) return the new value:

```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```

In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.

Affected code:

```
File: BlurExchange.sol
198:     function cancelOrders(Order[] calldata orders) external {
199:         for (uint8 i = 0; i < orders.length; i++) {
200:             cancelOrder(orders[i]);
201:         }
202:     }

File: BlurExchange.sol
476:         for (uint8 i = 0; i < fees.length; i++) {

File: PolicyManager.sol
77:         for (uint256 i = 0; i < length; i++) {

File: EIP712.sol
77:         for (uint256 i = 0; i < fees.length; i++) {

File: MerkleVerifier.sol
38:         for (uint256 i = 0; i < proof.length; i++) {
```

# PUBLIC FUNCTIONS TO EXTERNAL

An external call cost is less expensive than one of a public function. The following functions could be set external to save gas and improve code quality (extracted from Slither).

```
File: BlurExchange.sol
181:     function cancelOrder(Order calldata order) public {
182:         /* Assert sender is authorized to cancel order. */
183:         require(msg.sender == order.trader);
184: 
185:         bytes32 hash = _hashOrder(order, nonces[order.trader]);
186: 
187:         if (!cancelledOrFilled[hash]) {
188:             /* Mark order as cancelled, preventing it from being matched. */
189:             cancelledOrFilled[hash] = true;
190:             emit OrderCancelled(hash);
191:         }
192:     }
```

# It costs more gas to initialize variables with their default value than letting the default value be applied

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address...). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with `for (uint256 i; i < numIterations; ++i) {`

Affected code:

```
File: BlurExchange.sol
199:         for (uint8 i = 0; i < orders.length; i++) {

File: BlurExchange.sol
476:         for (uint8 i = 0; i < fees.length; i++) {

File: PolicyManager.sol
77:         for (uint256 i = 0; i < length; i++) {

File: EIP712.sol
77:         for (uint256 i = 0; i < fees.length; i++) {

File: MerkleVerifier.sol
38:         for (uint256 i = 0; i < proof.length; i++) {

File: ReentrancyGuarded.sol
10:     bool reentrancyLock = false;
```

# STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

```
File: BlurExchange.sol
113:         weth = _weth;
```


# USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

```
File: BlurExchange.sol
57:     string public constant name = "Blur Exchange";
58:     string public constant version = "1.0";
59:     uint256 public constant INVERSE_BASIS_POINT = 10000;
```
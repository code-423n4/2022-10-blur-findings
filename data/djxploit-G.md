## Use custom error to save deployment gas

```
File:   contracts/BlurExchange.sol

139:        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140:        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
142:        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143:        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
228:        require(address(_policyManager) != address(0), "Address cannot be zero");
237:        require(_oracle != address(0), "Address cannot be zero");
318:        require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
482:        require(totalFee <= price, "Total amount of fees are more than the price");
```

## Using bools for storage incurs overhead

```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past.

```
File:      contracts/BlurExchange.sol

71:        mapping(bytes32 => bool) public cancelledOrFilled;

File:     contracts/ExecutionDelegate.sol

18:    mapping(address => bool) public contracts;
19:    mapping(address => bool) public revokedApproval;

```

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's or an array's value in a local storage variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
File:   contracts/BlurExchange.sol

//All below mentioned instances are second-time accesses. So the 1st access should be cached, and then used here below
// cache sell.order
139:        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
145:        (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType) = _canMatchOrders(sell.order, buy.order);
170:         sell.order,

// cache buy.order
140:        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
145:        (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType) = _canMatchOrders(sell.order, buy.order);
172:         buy.order,

// cache sell.order.trader
148:            sell.order.trader,
156:            sell.order.trader,
168:            sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,
169:            sell.order.listingTime > buy.order.listingTime ? sell.order.trader : buy.order.trader,

// cache buy.order.trader
149:            buy.order.trader,
157:            buy.order.trader,
168:            sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,
169:            sell.order.listingTime > buy.order.listingTime ? sell.order.trader : buy.order.trader,

// cache sell.order.listingTime and buy.order.listingTime
169:            sell.order.listingTime > buy.order.listingTime ? sell.order.trader : buy.order.trader,

// cache order.trader
185:            bytes32 hash = _hashOrder(order, nonces[order.trader]);

FIle:     contracts/PolicyManager.sol

// cache _whitelistedPolicies.length()
72:            length = _whitelistedPolicies.length() - cursor;

```

## <array>.length should not be looked up in every loop of a for-loop

The overheads outlined below are PER LOOP, excluding the first loop
- storage arrays incur a Gwarmaccess (100 gas)
- memory arrays use MLOAD (3 gas)
- calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

```
File:    contracts/BlurExchange.sol

199:        for (uint8 i = 0; i < orders.length; i++) {
476:        for (uint8 i = 0; i < fees.length; i++) {

File:    contracts/lib/OrderStructs.sol

38:         for (uint256 i = 0; i < proof.length; i++) {
```

## It costs more gas to initialise variables to zero than to let the default of zero be applied

```
File:   contracts/BlurExchange.sol

199:        for (uint8 i = 0; i < orders.length; i++) {
475:        uint256 totalFee = 0;
476:        for (uint8 i = 0; i < fees.length; i++) {

File:     contracts/PolicyManager.sol

77:         for (uint256 i = 0; i < length; i++) {

File:    contracts/lib/OrderStructs.sol

38:         for (uint256 i = 0; i < proof.length; i++) {
```

## Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
File:     contracts/BlurExchange.sol

199:        for (uint8 i = 0; i < orders.length; i++) {
476:        for (uint8 i = 0; i < fees.length; i++) {
347:        uint8 v,
383:        uint8 v; bytes32 r; bytes32 s;
388:        (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

## Usage of `>=` or `<=` is cheaper than `>` or `<`

```
File:    contracts/BlurExchange.sol

169:            sell.order.listingTime > buy.order.listingTime ? sell.order.trader : buy.order.trader,
557:            return size > 0;
```

## `++i` costs less gas than `i++`, especially when it's used in for-loops

```
File:     contracts/BlurExchange.sol

199:        for (uint8 i = 0; i < orders.length; i++) {
476:        for (uint8 i = 0; i < fees.length; i++) {

File:     contracts/PolicyManager.sol

77:         for (uint256 i = 0; i < length; i++) {

File:    contracts/lib/OrderStructs.sol

38:         for (uint256 i = 0; i < proof.length; i++) {
```

## Internal functions only called once can be inlined to save gas

```
File:     contracts/BlurExchange.sol

Function `_exists` (#L548-L558) is called only once in #L534, hence should be inlined to save gas
Similarly, `_canMatchOrders` function, `_executeTokenTransfer` function, `_transferFees` function, `_executeFundsTransfer` function, `_validateOracleAuthorization` function, `_validateUserAuthorization` function and `_canSettleOrder` function can also be inlined, as they are called only once

```
## Changing function visibility from public to external saves gas

```
File:     contracts/BlurExchange.sol

`initialize` function needs to be marked external to save gas, as it's not called inside the contract
```

## Use unchecked keyword to avoid implicit underflow/overflow checks wherever appropriate

```
File:     contracts/BlurExchange.sol

199:        for (uint8 i = 0; i < orders.length; i++) {
476:        for (uint8 i = 0; i < fees.length; i++) {

File:     contracts/PolicyManager.sol

77:         for (uint256 i = 0; i < length; i++) {

File:    contracts/lib/OrderStructs.sol

38:         for (uint256 i = 0; i < proof.length; i++) {
```

## Multiple mappings can be combined into a single mapping, where appropriate**

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.


```
File:     contracts/ExecutionDelegate.sol

18:    mapping(address => bool) public contracts;
19:    mapping(address => bool) public revokedApproval;
```

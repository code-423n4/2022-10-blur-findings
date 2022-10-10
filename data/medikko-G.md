### [G-01] Use defualt value rather than overwriite variable with their default value.

Overriting varibles with defualt values with their default value will waste only gas and not necessary.

_There are **7** instances of this issue:_

File: BlurExchange.sol lines 199, 475, 476

```
for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

```
uint256 totalFee = 0;
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475

```
for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

File: PolicyManager.sol line 77

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77

File: EIP712.sol line 77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

File: MarkleVarifier.sol line 38

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

File: ReentrancyGuarded.sol line 10

```
bool reentrancyLock = false;
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10

### [G-02] In for loop use outside variable for array length

For loop written like this`for (uint256 i; i < array.length; ++i) {` will cost more gas than `for (uint256 i; i < _lengthOfArray; ++i) {` because for every iteration we use mload and memory_offset
that will cost about 6 gas

_There are **4** instances of this issue:_

File: BlurExchange.sol lines 199, 476

```
for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

```
for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

File: EIP712.sol line 77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

File: MerkleVerifier.sol line 38

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

### [G-03] Recommend to use short require string to save deployment gas

If you use longer require strings than 32 bytes that will cost more expensive than short require string.

_There are **2** instances of this issue:_

File: BlurExchange.sol line 482

```
require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482

File: ExecutionDelegate.sol line 22

```
require(contracts[msg.sender], "Contract is not approved to make transfers");
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22

### [G-04] i++, i += 1 and i = i + 1 IN FOR LOOPS COSTS MORE GAS COMPARED TO ++i

++i will save about 5 gas for each iteration

_There are **5** instances of this issue:_

File: BlurExchange.sol lines 199, 475, 476

```
for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

```
for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

File: PolicyManager.sol line 77

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77

File: EIP712.sol line 77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

File: MarkleVarifier.sol line 38

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

### [G-05] Use `uncheck()` in for loops whenere overflow and undeflow is not possible

Using of `uncheck(i++)`/`uncheck(++i)` will save about 30 gas per iteration because compiler not save check everytime. This feature come from 0.8


_There are **5** instances of this issue:_

File: BlurExchange.sol lines 199, 475, 476

```
for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

```
for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

File: PolicyManager.sol line 77

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77

File: EIP712.sol line 77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

File: MarkleVarifier.sol line 38

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

### [G-06] Recomend to use `uint256(1)`/`uint256(2)` for true and false

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. Also boolean from true to false cost 20000 gas rather than uint256(2) to uint256(1) that cost less.

_There have **5** istance of this issues:_

File: BlurExchange.sol line 71

```
mapping(bytes32 => bool) public cancelledOrFilled;
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L71

File: ExecutionDelegate.sol lines 18, 19

```
mapping(address => bool) public contracts;
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/ExecutionDelegate.sol#L18

```
mapping(address => bool) public revokedApproval;
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/ExecutionDelegate.sol#L19

File: ReentrancyGuarded.sol line 10

```
bool reentrancyLock = false;
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/lib/ReentrancyGuarded.sol#L10

### [G-07] Recomend to use one operator for comparison

If you use `>=` or `<=` this will cost more gas because in the EVM there is no implementation of Opcodes for `>=` and `<=`and two operations are done. You can use instead `x < y + 1 ` or `x + 1 > y` and save some gas.

_There have **3** istance of this issues:_

File: BlurExchange.sol lines 168, 422, 482

```
sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L168

```
if (sell.listingTime <= buy.listingTime) {
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L422

```
require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L482

### [G-08] = + cost less than += for state variables

_There have **1** istance of this issues:_

File: BlurExchange.sol line 479

```
totalFee += fee;
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L479

### [G-09] Using of custom errors than are came from 0.8.4 instead of `require()`/`revert()` strings will save deployment gas

_There have **10** istance of this issues:_

File: BlurExchange.sol lines 36, 134, 139, 140, 142, 143, 183, 219, 228, 237, 318, 407, 424, 428, 431, 452, 482, 534

```
require(isOpen == 1, "Closed");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L36

```
require(sell.order.side == Side.Sell);
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L134

```
require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L139

```
require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L140

```
require(_validateSignatures(sell, sellHash), "Sell failed authorization");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L142

```
require(_validateSignatures(buy, buyHash), "Buy failed authorization");
```
https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L143

```
require(msg.sender == order.trader);
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L183

```
require(address(_executionDelegate) != address(0), "Address cannot be zero");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L219

```
require(address(_policyManager) != address(0), "Address cannot be zero");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L228

```
  237:         require(_oracle != address(0), "Address cannot be zero");
```

https://github.com/code-423n4/2022-10-blur/blob/ad6c43bf2da2cee806e1dee20b6d05f651809ecc/contracts/BlurExchange.sol#L23

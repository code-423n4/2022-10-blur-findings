# Gas Report
## Optimizations found [9]

### [G-01] Variables Do Not Need to Be Initialized to Their Default Values [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#3--there-is-no-need-to-initialize-variables-with-default-values)

#### Findings:

*/contracts/lib/ReentrancyGuarded.sol*
Line(s): [10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)
```
10:	bool reentrancyLock = false;
```

**Suggested change:** 
```
10:	bool reentrancyLock;
```

*/contracts/BlurExchange.sol*
Line(s): [199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199), [475](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475), [476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
```
199:	for (uint8 i = 0; i < orders.length; i++) {
475:	uint256 totalFee = 0;
476:	for (uint8 i = 0; i < fees.length; i++) {
```

*/contracts/lib/MerkleVerifier.sol*
Line(s): [38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

```
38:	for (uint256 i = 0; i < proof.length; i++) {
```

*/contracts/PolicyManager.sol*
Line(s): [77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77) (small deployment cost saving)
```
77:	for (uint256 i = 0; i < length; i++) {
```

### [G-02] ++i is cheaper than i++ [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-12-i-costs-less-gas-compared-to-i-or-i--1)

#### Findings:

*/contracts/lib/EIP712.sol*
Line(s): [77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
```
77:	for (uint256 i = 0; i < fees.length; i++) {
```

*/contracts/BlurExchange.sol*
Line(s): [199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199), [476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
```
199:	for (uint8 i = 0; i < orders.length; i++) {
476:	for (uint8 i = 0; i < fees.length; i++) {
```

*/contracts/lib/MerkleVerifier.sol*
Line(s): [38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

```
38:	for (uint256 i = 0; i < proof.length; i++) {
```

*/contracts/PolicyManager.sol*
Line(s): [77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77) (if called externally from a contract)

```
77:	for (uint256 i = 0; i < proof.length; i++) {
```

### [G-03] Unless Used for Variable Packing uint8 May Be More Expensive Than Using uint256 [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-15-usage-of-uint8-may-increase-gas-cost)

#### Findings:

*/contracts/BlurExchange.sol*
Line(s): [199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199), [476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
```
199:	for (uint8 i = 0; i < orders.length; i++) {
476:	for (uint8 i = 0; i < fees.length; i++) {
```

#### Suggested change: 
```
199:	for (uint256 i; i < orders.length; ++i) {
476:	for (uint256 i; i < fees.length; ++i) {
```

### [G-04] Contants Can Be Made Private to Save Gas

Making constants private saves gas from getter generation. If one wants to access the constant value one can verify the source.

#### Findings:

*/contracts/BlurExchange.sol*
Line(s): [57](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57), [58](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58), [59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)
```
57:	string public constant name = "Blur Exchange";
58:	string public constant version = "1.0";
59:	uint256 public constant INVERSE_BASIS_POINT = 10000;
```

### [G-05] Use Calldata Instead of Memory for Read-Only Arguments [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#23--use-calldata-instead-of-memory-for-function-parameters)

#### Findings

*/contracts/lib/MerkleVerifier.sol*
Line(s): [20](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L20), [35](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L35)

```
20:	bytes32[] memory proof
35:	bytes32[] memory proof
```

### [G-06] Using Bools in Storage Incurs More Overhead Than unint256 [ⓘ](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)

#### Findings

*/contracts/lib/ReentrancyGuarded.sol*
Line(s): [10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10) / [15](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L15) / [17](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L17) 
```
10:	bool reentrancyLock = false;
15:	reentrancyLock = true;
17:	reentrancyLock = false;
```

**Suggested change:** 
```
10:	uint256 reentrancyLock;
15:	reentrancyLock = 1; //true
17:	reentrancyLock = 0; //false
```


### [G-07] Use Unchecked for Arithmetic That Cannot Underflow / Overflow [ⓘ](https://github.com/KadenZipfel/gas-optimizations/blob/main/gas-golfing/unchecked-arithmetic.md)

#### Findings

*/contracts/BlurExchange.sol*
Line(s): [482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482) / [485](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L485)

The check on [L482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)  makes it impossible for `recieveAmount` to underflow / overflow. For solidity >= 0.8.0 the unchecked keyword can be used to prevent the unneeded overflow / underflow check (using less gas).

```
482:	require(totalFee <= price, "Total amount of fees are more than the price");
485:	uint256 receiveAmount = price - totalFee;
```

**Suggested change:**
```
uint256 receiveAmount;
unchecked{ receiveAmount = price - totalFee; }
```

Line(s): [199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199), [476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

```
199:	for (uint8 i = 0; i < orders.length; i++) {
476:	for (uint8 i = 0; i < fees.length; i++) {
```

Note:  Assuming `i` is changed from a uint8 to a uint256 for [L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199) and [L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476).

*/contracts/lib/EIP712.sol*
Line(s): [77-79](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77-L79)

```
77:	for (uint256 i = 0; i < fees.length; i++) {
78:	    feeHashes[i] = _hashFee(fees[i]);
79:	}
```

**Suggested change:** 
```
for (uint256 i; i < fees.length;) {
    feeHashes[i] = _hashFee(fees[i]);
    unchecked{ ++i; }
}
```

*/contracts/PolicyManager.sol*
Line(s): [77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77) (if called externally from a contract)

```
77:	for (uint256 i = 0; i < proof.length; i++) {
```

### [G-08] Nesting if Statements Can Save Gas [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#6--use-nested-if-and-avoid-multiple-check-combinations)

#### Findings

*/contracts/BlurExchange.sol*
Line(s): [283](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L283)

```
283:	return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
```

**Suggested change:** 
Using De Morgan's laws: A /\ (B V C) = A /\ ~(~B /\ ~C)
```
if (listingTime >= block.timestamp) {
    return false;
}
if (expirationTime != 0) {
    if (block.timestamp >= expirationTime) {
        return false;
    }
}
return true;
```
**Trade-offs:**
This will increase deployment cost and result in less readable code. See G-09 for more gas saving.

### [G-09] Inline Internal Functions 

#### Findings

*/contracts/BlurExchange.sol*
Line(s): [283](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L283)

Gas can be saved by in-lining `_canSettleOrder` in its only function call  `_validateOrderParameters` (nesting).

```
function _validateOrderParameters(Order calldata order, bytes32 orderHash)
    internal
    view
    returns (bool)
{
    if (order.trader != address(0)) {
        if (cancelledOrFilled[orderHash] == false) {
            if (order.listingTime >= block.timestamp) {
                return false;
            }
            if (order.expirationTime != 0) {
                if (block.timestamp >= order.expirationTime) {
                    return false;
                }
            }
            return true;
        }
    }
    return false;
}
```
**Trade-offs:**
Results in less readable code.
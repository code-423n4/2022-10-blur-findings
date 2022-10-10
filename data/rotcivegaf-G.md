# Gas report

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 5 |
| [G&#x2011;02] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 5 |
| [G&#x2011;03] | `<array>.length` should not be looked up in every loop of a `for`-loop | 1 |
| [G&#x2011;04] | Don't compare boolean expressions to boolean literals | 4 |
| [G&#x2011;05] | No need to initialize variables with default values | 6 |
| [G&#x2011;06] | Increment in event emit | 1 |
| [G&#x2011;07] | Use function parameter to avoid `MLOAD` | 4 |

Total: 31 instances over 7 issues

## [G-01] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)

Saves 5 gas per loop

```solidity
File: /contracts/BlurExchange.sol

199        for (uint8 i = 0; i < orders.length; i++) {

476        for (uint8 i = 0; i < fees.length; i++) {
```

```solidity
File: /contracts/PolicyManager.sol

77        for (uint256 i = 0; i < length; i++) {
```

```solidity
File: /contracts/lib/EIP712.sol

77        for (uint256 i = 0; i < fees.length; i++) {
```

```solidity
File: /contracts/lib/MerkleVerifier.sol

38        for (uint256 i = 0; i < proof.length; i++) {
```

## [G‑02] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

```solidity
File: /contracts/BlurExchange.sol

199        for (uint8 i = 0; i < orders.length; i++) {

476        for (uint8 i = 0; i < fees.length; i++) {
```

```solidity
File: /contracts/PolicyManager.sol

77        for (uint256 i = 0; i < length; i++) {
```

```solidity
File: /contracts/lib/EIP712.sol

77        for (uint256 i = 0; i < fees.length; i++) {
```

```solidity
File: /contracts/lib/MerkleVerifier.sol

38        for (uint256 i = 0; i < proof.length; i++) {
```

## [G‑03] `<array>.length` should not be looked up in every loop of a `for`-loop

The overheads outlined below are PER LOOP, excluding the first loop

 * storage arrays incur a Gwarmaccess (100 gas)
 * memory arrays use `MLOAD` (3 gas)
 * calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset

```solidity
File: /contracts/lib/MerkleVerifier.sol

38        for (uint256 i = 0; i < proof.length; i++) {
```

## [G‑04] Don't compare boolean expressions to boolean literals

`require(<x> == true)` => `require(<x>)`
`require(<x> == false)` => `require(!<x>)`

```solidity
File: /contracts/ExecutionDelegate.sol

 77        require(revokedApproval[from] == false, "User has revoked approval");

 92        require(revokedApproval[from] == false, "User has revoked approval");

108        require(revokedApproval[from] == false, "User has revoked approval");

124        require(revokedApproval[from] == false, "User has revoked approval");
```

## [G-05] No need to initialize variables with default values

In solidity all variables initialize in 0, address(0), false, etc.

```solidity
File: /contracts/BlurExchange.sol

199        for (uint8 i = 0; i < orders.length; i++) {

475        uint256 totalFee = 0;

476        for (uint8 i = 0; i < fees.length; i++) {
```

```solidity
File: /contracts/PolicyManager.sol

77        for (uint256 i = 0; i < length; i++) {
```

```solidity
File: /contracts/lib/EIP712.sol

77        for (uint256 i = 0; i < fees.length; i++) {
```

```solidity
File: /contracts/lib/MerkleVerifier.sol

38        for (uint256 i = 0; i < proof.length; i++) {
```

## [G-06] Increment in event emit

```solidity
File: /contracts/BlurExchange.sol

From:
208        nonces[msg.sender] += 1;
209        emit NonceIncremented(msg.sender, nonces[msg.sender]);
To:
208        emit NonceIncremented(msg.sender, ++nonces[msg.sender]);
```

> Also can use `unchecked`

## [G-07] Use function parameter to avoid `MLOAD`

```solidity
File: /contracts/BlurExchange.sol

221        emit NewExecutionDelegate(executionDelegate);

230        emit NewPolicyManager(policyManager);

239        emit NewOracle(oracle);

247        emit NewBlockRange(blockRange);
```
# 2022-10-BLUR

## Gas Optimizations Report

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.

This change would save up to 6 gas per instance/loop.

_There are **5** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

199:  for (uint8 i = 0; i < orders.length; i++) {

476:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/lib/EIP712.sol

77:    for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```solidity
File: contracts/lib/MerkleVerifier.sol

38:    for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

```solidity
File: contracts/PolicyManager.sol

77:    for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

### State variables should be cached in stack variables rather than re-reading them.

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There are **1** instances of this issue:_

```solidity
File: contracts/PolicyManager.sol

      /// @audit Cache `_whitelistedPolicies`. Used 3 times in `viewWhitelistedPolicies`
71:    if (length > _whitelistedPolicies.length() - cursor) {
72:        length = _whitelistedPolicies.length() - cursor;
78:        whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

### `internal` and `private` functions that are called only once should be inlined.

The execution of a non-inlined function would cost up to 40 more gas because of two extra `jump`s as well as some other instructions.

_There are **2** instances of this issue:_

```solidity
File: contracts/lib/MerkleVerifier.sol

45:   function _hashPair(bytes32 a, bytes32 b) private pure returns (bytes32) {

49:   function _efficientHash(
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.

This change saves 3 gas per instance/loop

_There are **1** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

557:  return size > 0;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied

Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **7** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

199:  for (uint8 i = 0; i < orders.length; i++) {

475:  uint256 totalFee = 0;

476:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/lib/EIP712.sol

77:    for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```solidity
File: contracts/lib/MerkleVerifier.sol

38:    for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

```solidity
File: contracts/lib/ReentrancyGuarded.sol

10:   bool reentrancyLock = false;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol

```solidity
File: contracts/PolicyManager.sol

77:    for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

### Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **7** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

57:   string public constant name = "Blur Exchange";

58:   string public constant version = "1.0";

59:   uint256 public constant INVERSE_BASIS_POINT = 10000;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/lib/EIP712.sol

20:   bytes32 constant public FEE_TYPEHASH = keccak256(

23:   bytes32 constant public ORDER_TYPEHASH = keccak256(

26:   bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(

29:   bytes32 constant public ROOT_TYPEHASH = keccak256(
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **8** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

43:   function open() external onlyOwner {

47:   function close() external onlyOwner {

53:   function _authorizeUpgrade(address) internal override onlyOwner {}

181:  function cancelOrder(Order calldata order) public {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/ExecutionDelegate.sol

36:   function approveContract(address _contract) onlyOwner external {

45:   function denyContract(address _contract) onlyOwner external {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```solidity
File: contracts/PolicyManager.sol

25:   function addPolicy(address policy) external override onlyOwner {

36:   function removePolicy(address policy) external override onlyOwner {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **5** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

199:  for (uint8 i = 0; i < orders.length; i++) {

476:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/lib/EIP712.sol

77:    for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```solidity
File: contracts/lib/MerkleVerifier.sol

38:    for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

```solidity
File: contracts/PolicyManager.sol

77:    for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead

'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **11** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

199:  for (uint8 i = 0; i < orders.length; i++) {

347:  uint8 v,

383:  uint8 v; bytes32 r; bytes32 s;

388:      (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));

403:  uint8 v,

476:  for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/lib/EIP712.sol

21:    "Fee(uint16 rate,address recipient)"

24:    "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"

27:    "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```solidity
File: contracts/lib/OrderStructs.sol

9:    uint16 rate;

32:   uint8 v;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol

### Don't compare boolean expressions to boolean literals

Use `if(x)`/`if(!x)` instead of `if(x == true)`/`if(x == false)`.

_There are **5** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

267:      (cancelledOrFilled[orderHash] == false) &&
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/ExecutionDelegate.sol

77:    require(revokedApproval[from] == false, "User has revoked approval");

92:    require(revokedApproval[from] == false, "User has revoked approval");

108:  require(revokedApproval[from] == false, "User has revoked approval");

124:  require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

### Use `calldata` instead of `memory` for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **3** instances of this issue:_

```solidity
File: contracts/lib/EIP712.sol

39:   function _hashDomain(EIP712Domain memory eip712Domain)
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```solidity
File: contracts/lib/MerkleVerifier.sol

20:    bytes32[] memory proof

35:    bytes32[] memory proof
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **3** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

168:      sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,

422:  if (sell.listingTime <= buy.listingTime) {

482:  require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **2** instances of this issue:_

```solidity
File: contracts/lib/EIP712.sol

37:   bytes32 DOMAIN_SEPARATOR;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```solidity
File: contracts/PolicyManager.sol

16:   EnumerableSet.AddressSet private _whitelistedPolicies;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

### `require()/revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

_There are **2** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

482:  require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/ExecutionDelegate.sol

22:    require(contracts[msg.sender], "Contract is not approved to make transfers");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **24** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

36:    require(isOpen == 1, "Closed");

139:  require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

140:  require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

142:  require(_validateSignatures(sell, sellHash), "Sell failed authorization");

143:  require(_validateSignatures(buy, buyHash), "Buy failed authorization");

219:  require(address(_executionDelegate) != address(0), "Address cannot be zero");

228:  require(address(_policyManager) != address(0), "Address cannot be zero");

237:  require(_oracle != address(0), "Address cannot be zero");

318:      require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

407:  require(v == 27 || v == 28, "Invalid v parameter");

424:      require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

428:      require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

431:  require(canMatch, "Orders cannot be matched");

482:  require(totalFee <= price, "Total amount of fees are more than the price");

513:      revert("Invalid payment token");

534:  require(_exists(collection), "Collection does not exist");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/ExecutionDelegate.sol

22:    require(contracts[msg.sender], "Contract is not approved to make transfers");

77:    require(revokedApproval[from] == false, "User has revoked approval");

92:    require(revokedApproval[from] == false, "User has revoked approval");

108:  require(revokedApproval[from] == false, "User has revoked approval");

124:  require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```solidity
File: contracts/lib/ReentrancyGuarded.sol

14:    require(!reentrancyLock, "Reentrancy detected");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol

```solidity
File: contracts/PolicyManager.sol

26:    require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

37:    require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

### Using bools for storage variables incurs overhead

Use `uint256(1)` and `uint256(2)` for `true`/`false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

_There are **1** instances of this issue:_

```solidity
File: contracts/lib/ReentrancyGuarded.sol

10:   bool reentrancyLock = false;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol
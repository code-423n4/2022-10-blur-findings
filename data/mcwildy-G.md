# GAS OPTIMIZATIONS REPORT

## BLUR

### `x < y + 1` is cheaper than `x <= y`

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L168: sell.order.listingTime <= buy.order.listingTime ? sell.order.trader : buy.order.trader,

line#L422: if (sell.listingTime <= buy.listingTime) {

line#L482: require(totalFee <= price, "Total amount of fees are more than the price");
```

### Mark functions as `payable` to avoid the low-level evm `is-a-payment-provided` check

The EVM checks whether a payment is provided to the function and this check costs additional gas. If the function is marked as `payable` there is no such check

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L43:  function open() external onlyOwner {

line#L47:  function close() external onlyOwner {

line#L53:  function _authorizeUpgrade(address) internal override onlyOwner {}

line#L181: function cancelOrder(Order calldata order) public {
```

[ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol):

```solidity
line#L36:  function approveContract(address _contract) onlyOwner external {

line#L45:  function denyContract(address _contract) onlyOwner external {
```

[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol):

```solidity
line#L25:  function addPolicy(address policy) external override onlyOwner {

line#L36:  function removePolicy(address policy) external override onlyOwner {
```

### Use custom errors instead of `require` strings

[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol):

```solidity
line#L26:  require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

line#L37:  require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L36:  require(isOpen == 1, "Closed");

line#L139: require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

line#L142: require(_validateSignatures(sell, sellHash), "Sell failed authorization");

line#L143: require(_validateSignatures(buy, buyHash), "Buy failed authorization");

line#L228: require(address(_policyManager) != address(0), "Address cannot be zero");

line#L237: require(_oracle != address(0), "Address cannot be zero");

line#L407: require(v == 27 || v == 28, "Invalid v parameter");

line#L424: require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

line#L431: require(canMatch, "Orders cannot be matched");

line#L482: require(totalFee <= price, "Total amount of fees are more than the price");

line#L534: require(_exists(collection), "Collection does not exist");
```

[ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol):

```solidity
line#L22:  require(contracts[msg.sender], "Contract is not approved to make transfers");

line#L77:  require(revokedApproval[from] == false, "User has revoked approval");

line#L108: require(revokedApproval[from] == false, "User has revoked approval");

line#L124: require(revokedApproval[from] == false, "User has revoked approval");
```

[ReentrancyGuarded.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol):

```solidity
line#L14:  require(!reentrancyLock, "Reentrancy detected");
```

### Use `unchecked` for the counter in `for` loops

Since it is compared to a target value (usually the length of an array) there is no way the counter overflows

[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol):

```solidity
line#L77:  for (uint256 i = 0; i < fees.length; i++) {
```

[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol):

```solidity
line#L77:  for (uint256 i = 0; i < length; i++) {
```

[MerkleVerifier.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol):

```solidity
line#L38:  for (uint256 i = 0; i < proof.length; i++) {
```

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L199: for (uint8 i = 0; i < orders.length; i++) {

line#L476: for (uint8 i = 0; i < fees.length; i++) {
```

### Cache storage variables in memory

Optimization comes from using MSTORE and MLOAD instead of SSTORE and SLOAD

[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol):

```solidity
           /// @audit Cache `_whitelistedPolicies`. Used 3 times in `viewWhitelistedPolicies`
line#L71:  if (length > _whitelistedPolicies.length() - cursor) {
line#L72:  length = _whitelistedPolicies.length() - cursor;
line#L78:  whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
```

### Function inlining

If a function is called only once it can be inlined in order to save 20 gas

[MerkleVerifier.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol):

```solidity
line#L45:  function _hashPair(bytes32 a, bytes32 b) private pure returns (bytes32) {

line#L49:  function _efficientHash(
```

### Use `1` and `2` instead of `true` and `false`

EVM word size is 32 bytes. The `uint256 1` and `uint256 2` will be cheaper to use than the `boolean true` and `boolean false` since the first are 32 bytes long and the last are only 1 byte

[ReentrancyGuarded.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol):

```solidity
line#L10:  bool reentrancyLock = false;
```

### Use `calldata` where possible instead of `memory` to save gas

[MerkleVerifier.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol):

```solidity
line#L20:  bytes32[] memory proof

line#L35:  bytes32[] memory proof
```

[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol):

```solidity
line#L39:  function _hashDomain(EIP712Domain memory eip712Domain)
```

### `++i` costs less gas than `i++` (same for `--i`/`i--`)

Prefix increments are cheaper than postfix increments

[MerkleVerifier.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol):

```solidity
line#L38:  for (uint256 i = 0; i < proof.length; i++) {
```

[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol):

```solidity
line#L77:  for (uint256 i = 0; i < length; i++) {
```

[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol):

```solidity
line#L77:  for (uint256 i = 0; i < fees.length; i++) {
```

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L199: for (uint8 i = 0; i < orders.length; i++) {

line#L476: for (uint8 i = 0; i < fees.length; i++) {
```

### Use `private` visibility modifiers instead of `public` for storage `constant`s

Saves 3000+ gas per variable when deploying contract

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L57:  string public constant name = "Blur Exchange";

line#L58:  string public constant version = "1.0";

line#L59:  uint256 public constant INVERSE_BASIS_POINT = 10000;
```

[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol):

```solidity
line#L20:  bytes32 constant public FEE_TYPEHASH = keccak256(

line#L23:  bytes32 constant public ORDER_TYPEHASH = keccak256(

line#L26:  bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(

line#L29:  bytes32 constant public ROOT_TYPEHASH = keccak256(
```

### Use `if (x != 0)` instead of `if (x > 0)` for uints comparison

Saves 3 gas per `if`-check

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L557: return size > 0;
```

### Do not compare a boolean to `true` or `false`

Directly use the boolean

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L267: (cancelledOrFilled[orderHash] == false) &&
```

[ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol):

```solidity
line#L77:  require(revokedApproval[from] == false, "User has revoked approval");

line#L92:  require(revokedApproval[from] == false, "User has revoked approval");

line#L108: require(revokedApproval[from] == false, "User has revoked approval");

line#L124: require(revokedApproval[from] == false, "User has revoked approval");
```

### Do not write default values to variables

If you declare a variables of type `uint256` it will automatically get assigned to its default value of 0. It is a gas wastage to assign it yourself.

[ReentrancyGuarded.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol):

```solidity
line#L10:  bool reentrancyLock = false;
```

[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol):

```solidity
line#L77:  for (uint256 i = 0; i < length; i++) {
```

[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol):

```solidity
line#L77:  for (uint256 i = 0; i < fees.length; i++) {
```

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L199: for (uint8 i = 0; i < orders.length; i++) {

line#L475: uint256 totalFee = 0;

line#L476: for (uint8 i = 0; i < fees.length; i++) {
```

[MerkleVerifier.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol):

```solidity
line#L38:  for (uint256 i = 0; i < proof.length; i++) {
```

### Use `uint256` instead of the smaller uints where possible

The word-size in ethereum is 32 bytes which means that every variables which is sized with less bytes will cost more to operate with

[OrderStructs.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol):

```solidity
line#L9:   uint16 rate;

line#L32:  uint8 v;
```

[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol):

```solidity
line#L21:  "Fee(uint16 rate,address recipient)"

line#L24:  "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"

line#L27:  "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
```

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L199: for (uint8 i = 0; i < orders.length; i++) {

line#L347: uint8 v,

line#L388: (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));

line#L403: uint8 v,
```

### Use `constant` and `immutable` keywords for storage varibles if they doesn't change through contract's lifecycle

That way the variable will go to the contract's bytecode and will not allocate a storage slot

[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol):

```solidity
line#L37:  bytes32 DOMAIN_SEPARATOR;
```

[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol):

```solidity
line#L16:  EnumerableSet.AddressSet private _whitelistedPolicies;
```

### Do not use strings longer than 32 bytes

[ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol):

```solidity
line#L22:  require(contracts[msg.sender], "Contract is not approved to make transfers");
```

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L482: require(totalFee <= price, "Total amount of fees are more than the price");
```

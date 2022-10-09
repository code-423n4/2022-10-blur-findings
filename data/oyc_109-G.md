## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
2022-10-blur/contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## [G-03] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2022-10-blur/contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
2022-10-blur/contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
```

## [G-04] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-10-blur/contracts/BlurExchange.sol::43 => function open() external onlyOwner {
2022-10-blur/contracts/BlurExchange.sol::47 => function close() external onlyOwner {
2022-10-blur/contracts/PolicyManager.sol::25 => function addPolicy(address policy) external override onlyOwner {
2022-10-blur/contracts/PolicyManager.sol::36 => function removePolicy(address policy) external override onlyOwner {
```

## [G-05] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-10-blur/contracts/BlurExchange.sol::53 => function _authorizeUpgrade(address) internal override onlyOwner {}
```

## [G-06] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-10-blur/contracts/BlurExchange.sol::383 => uint8 v; bytes32 r; bytes32 s;
2022-10-blur/contracts/lib/OrderStructs.sol::9 => uint16 rate;
2022-10-blur/contracts/lib/OrderStructs.sol::32 => uint8 v;
```

## [G-07] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-10-blur/contracts/BlurExchange.sol::71 => mapping(bytes32 => bool) public cancelledOrFilled;
2022-10-blur/contracts/ExecutionDelegate.sol::18 => mapping(address => bool) public contracts;
2022-10-blur/contracts/ExecutionDelegate.sol::19 => mapping(address => bool) public revokedApproval;
```

## [G-08] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## [G-09] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-10-blur/contracts/BlurExchange.sol::208 => nonces[msg.sender] += 1;
2022-10-blur/contracts/BlurExchange.sol::479 => totalFee += fee;
```

## [G-10] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2022-10-blur/contracts/lib/EIP712.sol::45 => abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::61 => abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::91 => abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::107 => abi.encode(nonce)
2022-10-blur/contracts/lib/EIP712.sol::132 => keccak256(abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::147 => keccak256(abi.encode(
```

## [G-11] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-10-blur/contracts/BlurExchange.sol::36 => require(isOpen == 1, "Closed");
2022-10-blur/contracts/BlurExchange.sol::139 => require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
2022-10-blur/contracts/BlurExchange.sol::140 => require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
2022-10-blur/contracts/BlurExchange.sol::142 => require(_validateSignatures(sell, sellHash), "Sell failed authorization");
2022-10-blur/contracts/BlurExchange.sol::143 => require(_validateSignatures(buy, buyHash), "Buy failed authorization");
2022-10-blur/contracts/BlurExchange.sol::219 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::237 => require(_oracle != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::318 => require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
2022-10-blur/contracts/BlurExchange.sol::407 => require(v == 27 || v == 28, "Invalid v parameter");
2022-10-blur/contracts/BlurExchange.sol::424 => require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
2022-10-blur/contracts/BlurExchange.sol::428 => require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
2022-10-blur/contracts/BlurExchange.sol::431 => require(canMatch, "Orders cannot be matched");
2022-10-blur/contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
2022-10-blur/contracts/BlurExchange.sol::534 => require(_exists(collection), "Collection does not exist");
2022-10-blur/contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
2022-10-blur/contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::92 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::108 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::124 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/PolicyManager.sol::26 => require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
2022-10-blur/contracts/PolicyManager.sol::37 => require(_whitelistedPolicies.contains(policy), "Not whitelisted");
2022-10-blur/contracts/lib/ReentrancyGuarded.sol::14 => require(!reentrancyLock, "Reentrancy detected");
```

## [G-12] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## [G-13] Use bytes32 instead of string

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

```
2022-10-blur/contracts/BlurExchange.sol::57 => string public constant name = "Blur Exchange";
2022-10-blur/contracts/BlurExchange.sol::58 => string public constant version = "1.0";
```

## [G-14] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2022-10-blur/contracts/lib/ERC1967Proxy.sol::22 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```

## [G-15] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2022-10-blur/contracts/BlurExchange.sol::419 => returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
2022-10-blur/contracts/lib/ERC1967Proxy.sol::29 => function _implementation() internal view virtual override returns (address impl) {
```

## [G-16] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-10-blur/contracts/ExecutionDelegate.sol::18 => mapping(address => bool) public contracts;
2022-10-blur/contracts/ExecutionDelegate.sol::19 => mapping(address => bool) public revokedApproval;
```

## [G-17] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-10-blur/contracts/BlurExchange.sol::219 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::237 => require(_oracle != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::265 => (order.trader != address(0)) &&
```
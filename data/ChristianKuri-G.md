## [G001] Don't Initialize Variables with Default Value

#### Impact

Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecessary gas.

```solidity
// from
uint i = 0;
bool b = false;

// to
uint i;
bool b;
```

#### Findings:
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;
```

## [G002] Cache Array Length Outside of Loops

#### Impact

Cache the length of arrays in loops. **(~6 gas per iteration)**
The overheads outlined below are PER LOOP, excluding the first loop
 - storage arrays incur a Gwarmaccess (100 gas)
 - memory arrays use MLOAD (3 gas)
 - calldata arrays use CALLDATALOAD (3 gas)

```solidity
// from
for (uint256 i; i < array.length; ++i) {

// to
uint256 length = array.length;
for (uint256 i; i < length; ++i) {
```

#### Findings:
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## [G004] Functions guaranteed to revert when called by normal users can be marked as payable

#### Impact

If a function modifier suck as onlyOwner | onlyAdmin is used, the function will revert if a normal user tries to pay the function.
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are:
CALLVALUE (2 gas), DUP1(3 gas), ISZERO (3 gas), PUSH2(3 gas), JUMPI (10 gas), PUSH1(3 gas), DUPI(3 gas), REVERT(0), JUMPDEST (1 gas), POP (2 gas)
Total: **30 gas saved per call** approx to the function, in addition to the extra deployment cost.

```solidity
// from
function foo() external onlyOwner {

// to
function foo() external payable onlyOwner {
```

#### Findings:
```solidity
contracts/BlurExchange.sol::43 => function open() external onlyOwner {
contracts/BlurExchange.sol::47 => function close() external onlyOwner {
contracts/BlurExchange.sol::53 => function _authorizeUpgrade(address) internal override onlyOwner {}
contracts/ExecutionDelegate.sol::36 => function approveContract(address _contract) onlyOwner external {
contracts/ExecutionDelegate.sol::45 => function denyContract(address _contract) onlyOwner external {
contracts/PolicyManager.sol::25 => function addPolicy(address policy) external override onlyOwner {
contracts/PolicyManager.sol::36 => function removePolicy(address policy) external override onlyOwner {
```

## [G006] ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)

#### Impact

Use `++i` instead of `i++`, specially in loops. Find searching `i++` (~5 gas saved per iteration)
This is especially useful in for loops but this optimization can be used anywhere in your code. You can also use `unchecked{++i;}` for even more gas savings but this will not check to see if `i` overflows, which I do not recommend. For extra safety if you are worried about this, you can add a require statement after the loop checking if `i` is equal to the final incremented value. For best gas savings, use inline assembly, however this limits the functionality you can achieve. For example you cant use Solidity syntax to internally call your own contract within an assembly block and external calls must be done with the `call()` or `delegatecall()` instruction. However when applicable, inline assembly will save much more gas.

```solidity
// from
for (uint256 i; i < 100; i++) {

// to
for (uint256 i; i < 100; ++i) {
```

#### Findings:
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

## [G007] Use assembly to check for address 0

#### Impact

Checking for address 0 can be done with assembly, which is cheaper. **(~6 gas per instance)**

```solidity
// from
require(_addr != address(0));

// to
assembly {
  if iszero(_addr) {
    mstore(0x00, "zero address")
    revert(0x00, 0x20)
  }
}
```

#### Findings:
```solidity
contracts/BlurExchange.sol::219 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::237 => require(_oracle != address(0), "Address cannot be zero");
```

## [G009] Use `private` rather than `public` for constants

#### Impact

Using `private` rather than `public` for constants, saves gas **(~3,500 gas saved on deployment per constant)**
If needed, the value can be read from the verified contract source code. Saves ~3,500 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

```solidity
// from
uint256 public constant FOO = 1;

// to
uint256 private constant FOO = 1;
```

#### Findings:
```solidity
contracts/BlurExchange.sol::57 => string public constant name = "Blur Exchange";
contracts/BlurExchange.sol::58 => string public constant version = "1.0";
contracts/BlurExchange.sol::59 => uint256 public constant INVERSE_BASIS_POINT = 10000;
```

## [G011] `require()`/`revert()` strings longer than 32 bytes cost extra gas

#### Impact

`require()`/`revert()` strings longer than 32 bytes cost extra gas **(~3 gas saved per MSTORE)**
You can attach error reason strings along with require statements to make it easier to understand why a contract call reverted. These strings, however, take space in the deployed bytecode. Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive. Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

```solidity
// from
require(..., "Not Authorized. This function can only be called by the custodian or owner of this contract.");

// to
require(...);

// or
require(..., "Unauthorized");
```

#### Findings:
```solidity
contracts/BlurExchange.sol::318 => require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
```

## [G012] Use custom errors instead of revert strings

#### Impact

Use custom errors instead of revert strings. (~50 gas saved per call, ~5,500 gas saved on deployment)
Custom errors from Solidity 0.8.4 and upwards are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained [here](https://blog.soliditylang.org/2021/04/21/custom-errors/)

It not only saves gas upon deployment - ~5500 gas saved per custom error instead of a require statement, but it is also cheaper in a function call, custom errors save ~50 gas each time they're hit by avoiding having to allocate and store the revert string.

```solidity
// from
require(msg.sender != owner)

// to
error Unauthorized();
if (msg.sender != owner) {
  revert Unauthorized();
}
```

#### Findings:
```solidity
contracts/BlurExchange.sol::36 => require(isOpen == 1, "Closed");
contracts/BlurExchange.sol::134 => require(sell.order.side == Side.Sell);
contracts/BlurExchange.sol::139 => require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
contracts/BlurExchange.sol::140 => require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
contracts/BlurExchange.sol::142 => require(_validateSignatures(sell, sellHash), "Sell failed authorization");
contracts/BlurExchange.sol::143 => require(_validateSignatures(buy, buyHash), "Buy failed authorization");
contracts/BlurExchange.sol::183 => require(msg.sender == order.trader);
contracts/BlurExchange.sol::219 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::237 => require(_oracle != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::318 => require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
contracts/BlurExchange.sol::407 => require(v == 27 || v == 28, "Invalid v parameter");
contracts/BlurExchange.sol::424 => require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
contracts/BlurExchange.sol::428 => require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
contracts/BlurExchange.sol::431 => require(canMatch, "Orders cannot be matched");
contracts/BlurExchange.sol::452 => require(msg.value == price);
contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
contracts/BlurExchange.sol::534 => require(_exists(collection), "Collection does not exist");
contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::92 => require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::108 => require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::124 => require(revokedApproval[from] == false, "User has revoked approval");
contracts/PolicyManager.sol::26 => require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
contracts/PolicyManager.sol::37 => require(_whitelistedPolicies.contains(policy), "Not whitelisted");
contracts/lib/ReentrancyGuarded.sol::14 => require(!reentrancyLock, "Reentrancy detected");
```

## [G013] Use immutable variables instead of constants when using keccak256

#### Impact

Constant expressions are left as expressions, not constants.
The expression is re-calculated each time the constant is referenced.
Each usage of the `constant` costs **~100 gas more on each access**
Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library )
If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

```solidity
// from
bytes32 public constant FOO = keccak256("foo");

// to
bytes32 public immutable FOO = keccak256("foo");
```

#### Findings:
```solidity
contracts/lib/EIP712.sol::20 => bytes32 constant public FEE_TYPEHASH = keccak256(
contracts/lib/EIP712.sol::23 => bytes32 constant public ORDER_TYPEHASH = keccak256(
contracts/lib/EIP712.sol::26 => bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
contracts/lib/EIP712.sol::29 => bytes32 constant public ROOT_TYPEHASH = keccak256(
contracts/lib/EIP712.sol::33 => bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```
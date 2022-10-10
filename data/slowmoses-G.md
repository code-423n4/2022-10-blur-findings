## Use Pre Increment Instead of Post Increment

Pre-increment uses a little less gas.

***

for (uint256 i = 0; i < length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L77

for (uint256 i = 0; i < fees.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L77

for (uint8 i = 0; i < orders.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L199

for (uint8 i = 0; i < fees.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L476

for (uint256 i = 0; i < proof.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L38

## Cache Array Length

Array length should be cached instead of requesting it over and over in a for loop.
The cost per iteration depends on the array type, 100 gas for storage, 3 each for memory and calldata.
Caching the value costs only 3 gas.

***

for (uint256 i = 0; i < fees.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L77

for (uint8 i = 0; i < orders.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L199

for (uint8 i = 0; i < fees.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L476

for (uint256 i = 0; i < proof.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L38

## Use Custom Errors Instead of Strings for Revert and Require

Require strings and revert strings use about 50 more gas than custom errors.

***

require(!reentrancyLock, "Reentrancy detected");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ReentrancyGuarded.sol#L14

require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L26

require(_whitelistedPolicies.contains(policy), "Not whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L37

require(contracts[msg.sender], "Contract is not approved to make transfers");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L22

require(revokedApproval[from] == false, "User has revoked approval");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L77

require(revokedApproval[from] == false, "User has revoked approval");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L92

require(revokedApproval[from] == false, "User has revoked approval");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L108

require(revokedApproval[from] == false, "User has revoked approval");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L124

require(isOpen == 1, "Closed");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L36

require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L139

require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L140

require(_validateSignatures(sell, sellHash), "Sell failed authorization");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L142

require(_validateSignatures(buy, buyHash), "Buy failed authorization");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L143

require(address(_executionDelegate) != address(0), "Address cannot be zero");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L219

require(address(_policyManager) != address(0), "Address cannot be zero");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L228

require(_oracle != address(0), "Address cannot be zero");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L237

require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L318

require(v == 27 || v == 28, "Invalid v parameter");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L407

require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L424

require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L428

require(canMatch, "Orders cannot be matched");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L431

require(totalFee <= price, "Total amount of fees are more than the price");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L482

revert("Invalid payment token");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L513

require(_exists(collection), "Collection does not exist");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L534

## Limit Message Strings for Revert and Require to 32 Bytes

Require strings and revert strings longer than 32 bytes need MSTORE (3 gas).
Try to use shorter descriptions. Custom errors would be even better (see above).

***

require(contracts[msg.sender], "Contract is not approved to make transfers");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L22

require(totalFee <= price, "Total amount of fees are more than the price");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L482

## Do not Initialize to Default Value

Non-const variables should not be initialized to their default value.
Just using the default costs less gas.
For example use: uint256 abc;
Instead of: uint256 abc=0;

***

for (uint256 i = 0; i < length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L77

for (uint256 i = 0; i < fees.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L77

for (uint8 i = 0; i < orders.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L199

uint256 totalFee = 0;
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L475

for (uint8 i = 0; i < fees.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L476

for (uint256 i = 0; i < proof.length; i++) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L38

bool reentrancyLock = false;
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ReentrancyGuarded.sol#L10


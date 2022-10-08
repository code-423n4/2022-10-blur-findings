# c4udit Report

## Files analyzed
- contracts/BlurExchange.sol
- contracts/ExecutionDelegate.sol
- contracts/PolicyManager.sol
- contracts/lib/EIP712.sol
- contracts/lib/ERC1967Proxy.sol
- contracts/lib/MerkleVerifier.sol
- contracts/lib/OrderStructs.sol
- contracts/lib/ReentrancyGuarded.sol

## Issues found

### Don't Initialize Variables with Default Value

#### Impact
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::55 => return _whitelistedPolicies.length();
contracts/PolicyManager.sol::69 => uint256 length = size;
contracts/PolicyManager.sol::71 => if (length > _whitelistedPolicies.length() - cursor) {
contracts/PolicyManager.sol::72 => length = _whitelistedPolicies.length() - cursor; 
contracts/PolicyManager.sol::75 => address[] memory whitelistedPolicies = new address[](length);
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/PolicyManager.sol::81 => return (whitelistedPolicies, cursor + length);
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
contracts/BlurExchange.sol::557 => return size > 0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
contracts/BlurExchange.sol::508 => payable(to).transfer(amount);
contracts/ExecutionDelegate.sol::78 => IERC721(collection).transferFrom(from, to, tokenId);
contracts/ExecutionDelegate.sol::125 => return IERC20(token).transferFrom(from, to, amount);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)


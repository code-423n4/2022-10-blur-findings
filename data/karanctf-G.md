## [G-1] Use preincrement to save gas
```solidity
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

## [G-2] Cache .length in loops 
```solidity
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
contracts/lib/EIP712.sol:75:            fees.length
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol:55:        return _whitelistedPolicies.length();
contracts/PolicyManager.sol:71:        if (length > _whitelistedPolicies.length() - cursor) {
contracts/PolicyManager.sol:72:            length = _whitelistedPolicies.length() - cursor;
contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

## [G-3] Donot use default values Explicit initialization with zero is not required for variable declaration of numTokens because `uints are 0` by default.removeing this will reduce contract size and save a bit of gas.

```solidity
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/ReentrancyGuarded.sol:10:    bool reentrancyLock = false;
contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol:475:        uint256 totalFee = 0;
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

## [G-4] Variable == false|0 -> !variable or variable ==  true -> variable
```solidity
contracts/ExecutionDelegate.sol:77:        require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol:92:        require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol:108:        require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol:124:        require(revokedApproval[from] == false, "User has revoked approval");
contracts/BlurExchange.sol:267:            (cancelledOrFilled[orderHash] == false) &&
contracts/BlurExchange.sol:283:        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
contracts/BlurExchange.sol:316:        if (order.order.expirationTime == 0) {
contracts/BlurExchange.sol:502:        if (amount == 0) {
```

## [G-05] Use `!= 0` instead of `> 0`
```solidity
contracts/BlurExchange.sol:557:        return size > 0;
```
## [G-6]<x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
contracts/BlurExchange.sol:208:        nonces[msg.sender] += 1;
contracts/BlurExchange.sol:479:            totalFee += fee;
```
## [G-7] Initialise vairable outside of loop
```solidity=
contracts/BlurExchange.sol:477:            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
contracts/lib/MerkleVerifier.sol:39:            bytes32 proofElement = proof[i];

```
## [G-8] Use `10000`  in place of `INVERSE_BASIS_POINT` instead of reading it again and again inside for loop it won't change as it is initilized as constant
```solidity
contracts/BlurExchange.sol:477:            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;

```
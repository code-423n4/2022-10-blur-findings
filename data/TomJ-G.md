## Table of Contents
Total of 12 issues found.
- Should Use Unchecked Block where Over/Underflow is not Possible
- Defined Variables Used Only Once
- Duplicate require() Checks Should be a Modifier or a Function
- Unchanging State Variable Should be Immutable
- Change Function Visibility Public to External
- Internal Function Called Only Once can be Inlined
- Boolean Comparisons
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Store Array's Length as a Variable
- ++i Costs Less Gas than i++
- Keep The Revert Strings of Error Messages within 32 Bytes
- Use Custom Errors to Save Gas

&ensp;
### Should Use Unchecked Block where Over/Underflow is not Possible

#### Issue
Since Solidity 0.8.0, all arithmetic operations revert on overflow and underflow by default.
However in places where overflow and underflow is not possible, it is better to use unchecked block to reduce the gas usage.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#checked-or-unchecked-arithmetic

#### PoC
Total of 1 instance found.

1. `_transferFees()` of `BlurExchange.sol`
```
Wrap line 485 with unchecked since underflow is not possible due to line 482 check
482:        require(totalFee <= price, "Total amount of fees are more than the price");
485:        uint256 receiveAmount = price - totalFee;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482-L485

#### Mitigation
Wrap the code with uncheck like described in above PoC. 

&ensp;
### Defined Variables Used Only Once

#### Issue
Certain variables is defined even though they are used only once.
Remove these unnecessary variables to save gas.
For cases where it will reduce the readability, one can use comments to help describe
what the code is doing.

#### PoC
Total of 1 instance found.

1. Remove `computedRoot` variable of `_validateUserAuthorization()` of `BlurExchange.sol` 
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L361-L362
Mitigation:
Delete line 361 and replace line 362 with code shown below
```
hashToSign = _hashToSignRoot(MerkleVerifier._computeRoot(orderHash, merklePath));
```

#### Mitigation
Don't define variable that is used only once.
Details are listed on above PoC.

&ensp;
### Duplicate require() Checks Should be a Modifier or a Function

#### Issue
Since below require checks are used more than once,
I recommend making these to a modifier or a function.

#### PoC
Total of 1 instance found.
```
./ExecutionDelegate.sol:77:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:92:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:108:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:124:        require(revokedApproval[from] == false, "User has revoked approval");
```

#### Mitigation
I recommend making duplicate require statement into modifier or a function.

&ensp;
### Unchanging State Variable Should be Immutable

#### Issue
State variable that is only set in the constructor and can't be changed afterwards,
should be declared as immutable.

#### PoC
Total of 1 instance found.

1. `weth` variable of `BlurExchange.sol`
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L63

#### Mitigation
Change to immutable.

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 1 instance found.

`MerkleVerifier.sol`:`_verifyProof()`
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17

#### Mitigation
Change the function visibility to external.

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 7 instances found.

1. `_validateUserAuthorization()` of `BlurExchange.sol`
This function called only once at line 303
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L344
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L303

2. `_validateOracleAuthorization()` of `BlurExchange.sol`
This function called only once at line 320
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L375
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L320

3. `_canMatchOrders()` of `BlurExchange.sol`
This function called only once at line 145
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L416
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L145

4. `_executeFundsTransfer()` of `BlurExchange.sol`
This function called only once at line 147
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L444
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L147

5. `_transferFees()` of `BlurExchange.sol`
This function called only once at line 456
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L469
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L456

6. `_executeTokenTransfer()` of `BlurExchange.sol`
This function called only once at line 154
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L525
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L154

7. `_exists()` of `BlurExchange.sol`
This function called only once at line 534
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L548
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Boolean Comparisons

#### Issue
It is more gas expensive to compare boolean with "variable == true" or "variable == false" than 
directly checking the returned boolean value.

#### PoC
Total of 5 instances found.
```solidity
./BlurExchange.sol:267:            (cancelledOrFilled[orderHash] == false) &&
./ExecutionDelegate.sol:77:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:92:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:108:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:124:        require(revokedApproval[from] == false, "User has revoked approval");
```

#### Mitigation
Simply check by returned boolean value.
Change it to
```
require(!somethinghere)
```

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size. Therefore it is more gas saving to use 32 bytes 
unless it is used in a struct or state variable in order to reduce storage slot. 

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 7 instances found.
```
./BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
./BlurExchange.sol:347:        uint8 v,
./BlurExchange.sol:383:        uint8 v; bytes32 r; bytes32 s;
./BlurExchange.sol:385:            (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
./BlurExchange.sol:388:            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
./BlurExchange.sol:403:        uint8 v,
./BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### Store Array's Length as a Variable 

#### Issue
By storing an array's length as a variable before the for-loop,
can save 3 gas per iteration.

#### PoC
Total of 4 instances found.
```
./MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
./EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
./BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
./BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

#### Mitigation
Store array's length as a variable before looping it.
For example, I suggest changing it to
```
uint256 len = proof.length;
for (uint256 i = 0; i < len; i++) {
```

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 5 instances found.
```
./PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
./MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
./EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
./BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
./BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

#### Mitigation
Change it to postfix increments/decrements.
It saves 6 gas per loop.
For example,
```
for (uint256 i = 0; i < length; ++i) {
```

&ensp;
### Keep The Revert Strings of Error Messages within 32 Bytes

#### Issue
Since each storage slot is size of 32 bytes, error messages that is longer than this will need
extra storage slot leading to more gas cost.

#### PoC
Total of 2 instances found.
```solidity
./BlurExchange.sol:482:        require(totalFee <= price, "Total amount of fees are more than the price");
./ExecutionDelegate.sol:22:        require(contracts[msg.sender], "Contract is not approved to make transfers");
```

#### Mitigation
Simply keep the error messages within 32 bytes to avoid extra storage slot cost.

&ensp;
### Use Custom Errors to Save Gas

#### Issue
Custom errors from Solidity 0.8.4 are cheaper than revert strings.
Details are explained here: https://blog.soliditylang.org/2021/04/21/custom-errors/

#### PoC
Total of 23 instances found.
```
./PolicyManager.sol:26:        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
./PolicyManager.sol:37:        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
./ReentrancyGuarded.sol:14:        require(!reentrancyLock, "Reentrancy detected");
./BlurExchange.sol:36:        require(isOpen == 1, "Closed");
./BlurExchange.sol:139:        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
./BlurExchange.sol:140:        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
./BlurExchange.sol:142:        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
./BlurExchange.sol:143:        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
./BlurExchange.sol:219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
./BlurExchange.sol:228:        require(address(_policyManager) != address(0), "Address cannot be zero");
./BlurExchange.sol:237:        require(_oracle != address(0), "Address cannot be zero");
./BlurExchange.sol:318:            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
./BlurExchange.sol:407:        require(v == 27 || v == 28, "Invalid v parameter");
./BlurExchange.sol:424:            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
./BlurExchange.sol:428:            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
./BlurExchange.sol:431:        require(canMatch, "Orders cannot be matched");
./BlurExchange.sol:482:        require(totalFee <= price, "Total amount of fees are more than the price");
./BlurExchange.sol:534:        require(_exists(collection), "Collection does not exist");
./ExecutionDelegate.sol:22:        require(contracts[msg.sender], "Contract is not approved to make transfers");
./ExecutionDelegate.sol:77:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:92:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:108:        require(revokedApproval[from] == false, "User has revoked approval");
./ExecutionDelegate.sol:124:        require(revokedApproval[from] == false, "User has revoked approval");
```

#### Mitigation
I suggest implementing custom errors to save gas.

&ensp;
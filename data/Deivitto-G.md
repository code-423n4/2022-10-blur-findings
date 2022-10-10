
# GAS
## Constants expressions are expressions, not constants.
### Summary
Constant expressions are left as expressions, not constants.
### Details
Reference to this kind of issue: https://github.com/ethereum/solidity/issues/9232
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L57
    string public constant name = "Blur Exchange";

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L58
    string public constant version = "1.0";

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L59
    uint256 public constant INVERSE_BASIS_POINT = 10000;
### Mitigation
Use immutable for this expressions

## Public function visibility can be made external
### Summary
Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### Details
If a function is never called from the contract it should be marked as external. This will save gas.
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/MerkleVerifier.sol#L17-L26

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L95-L118

### Mitigation
Consider changing visibility from public to external.



## use of custom errors rather than revert() / require() error message
### Summary
Custom errors reduce 38 gas if the condition is met and 22 gas otherwise.
Also reduces contract size and deployment costs.
### Details
Since version 0.8.4 the use of custom errors rather than revert() / require() saves gas as noticed in
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://github.com/code-423n4/2022-04-pooltogether-findings/issues/13

### Github Permalinks

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ReentrancyGuarded.sol#L14
        require(!reentrancyLock, "Reentrancy detected");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L26
        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L37
        require(_whitelistedPolicies.contains(policy), "Not whitelisted");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L22
        require(contracts[msg.sender], "Contract is not approved to make transfers");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L77
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L92
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L108
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L124
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L36
        require(isOpen == 1, "Closed");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L139
        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L140
        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L142
        require(_validateSignatures(sell, sellHash), "Sell failed authorization");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L143
        require(_validateSignatures(buy, buyHash), "Buy failed authorization");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L219
        require(address(_executionDelegate) != address(0), "Address cannot be zero");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L228
        require(address(_policyManager) != address(0), "Address cannot be zero");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L237
        require(_oracle != address(0), "Address cannot be zero");



https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L318
            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L407
        require(v == 27 || v == 28, "Invalid v parameter");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L424
            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L428
            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L431
        require(canMatch, "Orders cannot be matched");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L482
        require(totalFee <= price, "Total amount of fees are more than the price");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L534
        require(_exists(collection), "Collection does not exist");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L513
            revert("Invalid payment token");


### Mitigation
replace each error message in a require by a custom error

## duplicated `require()` check should be refactored
### Summary
duplicated `require()` / `revert()` checks should be
refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

### Github Permalinks

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L77
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L92
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L108
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L124
        require(revokedApproval[from] == false, "User has revoked approval");

- - - -

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L219
        require(address(_executionDelegate) != address(0), "Address cannot be zero");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L228
        require(address(_policyManager) != address(0), "Address cannot be zero");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L237
        require(_oracle != address(0), "Address cannot be zero");

- - - -

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L424
            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L428
            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
- - - -


### Mitigation
refactor this checks to different functions to save gas

## Reduce the size of error messages (Long revert Strings)
### Summary
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L22

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L482

### Mitigation
Consider shortening the revert strings to fit in 32 bytes


## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L18-L19

### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.

## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/OrderStructs.sol#L13-L28

### Mitigation
Order structs to reduce gas usage.


## Using private rather than public for constants saves gas
### Summary
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L57
    string public constant name = "Blur Exchange";

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L58
    string public constant version = "1.0";

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L59
    uint256 public constant INVERSE_BASIS_POINT = 10000;

### Mitigation
Consider replacing public for private in constants for gas saving.

## Explicit initialization
### Summary
It is not needed to initialize variables to the default value. Explicitly initializing it with its default value is an anti-pattern and wastes gas.
### Details
If a variable is not set/initialized, it is assumed to have the default value ( 0 for uint, false for bool, address(0) for address…). 

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ReentrancyGuarded.sol#L10
    bool reentrancyLock = false;
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L475
        uint256 totalFee = 0;
### Mitigation
Don't initialize variables to default value

## Index initialized in for loop
### Summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L77
        for (uint256 i = 0; i < length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L77
        for (uint256 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L199
        for (uint8 i = 0; i < orders.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L476
        for (uint8 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/MerkleVerifier.sol#L38
        for (uint256 i = 0; i < proof.length; i++) {


### Mitigation
Don't initialize variables to default value



## ++i costs less gas compared to i++ or i+=1, the same happens with --i and i-- or i-=1
### Summary
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). 
This statement is true even with the optimizer enabled. 

### Details
i++ increments i and returns the initial value of i . 
Which means:
```
uint i = 1;
i++; // == 1 but i == 2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

### Github Permalinks
`+= 1`
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L208
        nonces[msg.sender] += 1;

`var++`
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L77
        for (uint256 i = 0; i < length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L77
        for (uint256 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L199
        for (uint8 i = 0; i < orders.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L476
        for (uint8 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/MerkleVerifier.sol#L38
        for (uint256 i = 0; i < proof.length; i++) {

### Mitigation
Replace to `++i` or `--i` as needed.


## increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L77
        for (uint256 i = 0; i < length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L77
        for (uint256 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L199
        for (uint8 i = 0; i < orders.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L476
        for (uint8 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/MerkleVerifier.sol#L38
        for (uint256 i = 0; i < proof.length; i++) {

### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header


## `<array>.length` should no be looked up in every loop of a for-loop
### Summary
In loops not assigning the length to a variable so memory accessed a lot (caching local variables)

### Details
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a `Gwarmaccess (100 gas)`
memory arrays use `MLOAD (3 gas)`
calldata arrays use `CALLDATALOAD (3 gas)`

### Github Permalinks

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L77
        for (uint256 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L199
        for (uint8 i = 0; i < orders.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L476
        for (uint8 i = 0; i < fees.length; i++) {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/MerkleVerifier.sol#L38
        for (uint256 i = 0; i < proof.length; i++) {


### Mitigation
Assign the length of the `array.length` to a local variable in loops for gas savings


## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
fees[i]
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L477-L478


### Mitigation
Cache variables used more than one into a local variable.


## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L25
    function addPolicy(address policy) external override onlyOwner {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L36
    function removePolicy(address policy) external override onlyOwner {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L36
    function approveContract(address _contract) onlyOwner external {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L45
    function denyContract(address _contract) onlyOwner external {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L43
    function open() external onlyOwner {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L47
    function close() external onlyOwner {

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L53
    function _authorizeUpgrade(address) internal override onlyOwner {}

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L217
        onlyOwner

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L226
        onlyOwner

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L235
        onlyOwner

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L244
        onlyOwner


### Mitigation
Consider adding payable to save gas

## `>=` cheaper than `>`
### Summary
Strict inequalities ( `>` ) are more expensive than non-strict ones ( `>=` ). This is due to some supplementary checks (`ISZERO`, 3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L557
        return size > 0;

### Mitigation
Consider using `>= 1` instead of `> 0` to avoid some opcodes


## `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for state variables
### Summary
`x+=y` costs more gas than x=x+y for state variables

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L208
        nonces[msg.sender] += 1;

### Mitigation
Don't use `+=` for state variables as it cost more gas.

## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the abi.encode function to `abi.encodePacked` can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.
### Github Permalinks

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L45
            abi.encode(

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L61
            abi.encode(

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L107
                abi.encode(nonce)

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L132
            keccak256(abi.encode(

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L147
            keccak256(abi.encode(

### Mitigation
Consider changing abi.encode to `abi.encodePacked`


## Using bools for storage incurs overhead { 
### Summary
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### Details
Here is one example of OpenZeppelin about this optimization
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ReentrancyGuarded.sol#L10

### Mitigation
Consider using uint256 with values 0 and 1 rather than bool



## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(executionDelegate);
    }
```

Recommendation
Change to:
```
    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(_executionDelegate);
    }
```
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L215-L222

- Same scenario on different variables in emit()
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L224-L248
### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas



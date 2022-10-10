### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
contracts/lib/EIP712.sol:L45            abi.encode(

contracts/lib/EIP712.sol:L61            abi.encode(

contracts/lib/EIP712.sol:L91                abi.encode(

contracts/lib/EIP712.sol:L107                abi.encode(nonce)

contracts/lib/EIP712.sol:L132            keccak256(abi.encode(

contracts/lib/EIP712.sol:L147            keccak256(abi.encode(

```

### [G-02] Explicit initialization with false not required


#### Impact
Explicit initialization with false is not required for variable declaration because bools are false by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
contracts/lib/ReentrancyGuarded.sol:L10    bool reentrancyLock = false;

```

### [G-03] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
contracts/ExecutionDelegate.sol:L18    mapping(address => bool) public contracts;

contracts/ExecutionDelegate.sol:L19    mapping(address => bool) public revokedApproval;

contracts/BlurExchange.sol:L71    mapping(bytes32 => bool) public cancelledOrFilled;

```

###  [G-04] Cache Array Length Outside of Loop


#### Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.


#### Findings:
```
contracts/BlurExchange.sol:L199        for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol:L476        for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol:L38        for (uint256 i = 0; i < proof.length; i++) {

contracts/lib/EIP712.sol:L77        for (uint256 i = 0; i < fees.length; i++) {

```

### [G-05] Use custom errors rather than ```revert()```/```require()``` string to save gas


#### Impact
Custom errors are available from solidity version 0.8.4, it saves around 50 gas each time they are hit by avoiding having to allocate and store the revert string.


#### Findings:
```
contracts/ExecutionDelegate.sol:L22        require(contracts[msg.sender], "Contract is not approved to make transfers");

contracts/ExecutionDelegate.sol:L77        require(revokedApproval[from] == false, "User has revoked approval");

contracts/ExecutionDelegate.sol:L92        require(revokedApproval[from] == false, "User has revoked approval");

contracts/ExecutionDelegate.sol:L108        require(revokedApproval[from] == false, "User has revoked approval");

contracts/ExecutionDelegate.sol:L124        require(revokedApproval[from] == false, "User has revoked approval");

contracts/PolicyManager.sol:L26        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

contracts/PolicyManager.sol:L37        require(_whitelistedPolicies.contains(policy), "Not whitelisted");

contracts/BlurExchange.sol:L36        require(isOpen == 1, "Closed");

contracts/BlurExchange.sol:L139        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");

contracts/BlurExchange.sol:L140        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

contracts/BlurExchange.sol:L142        require(_validateSignatures(sell, sellHash), "Sell failed authorization");

contracts/BlurExchange.sol:L143        require(_validateSignatures(buy, buyHash), "Buy failed authorization");

contracts/BlurExchange.sol:L219        require(address(_executionDelegate) != address(0), "Address cannot be zero");

contracts/BlurExchange.sol:L228        require(address(_policyManager) != address(0), "Address cannot be zero");

contracts/BlurExchange.sol:L237        require(_oracle != address(0), "Address cannot be zero");

contracts/BlurExchange.sol:L318            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

contracts/BlurExchange.sol:L407        require(v == 27 || v == 28, "Invalid v parameter");

contracts/BlurExchange.sol:L424            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

contracts/BlurExchange.sol:L428            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

contracts/BlurExchange.sol:L431        require(canMatch, "Orders cannot be matched");

contracts/BlurExchange.sol:L482        require(totalFee <= price, "Total amount of fees are more than the price");

contracts/BlurExchange.sol:L513            revert("Invalid payment token");

contracts/BlurExchange.sol:L534        require(_exists(collection), "Collection does not exist");

contracts/lib/ReentrancyGuarded.sol:L14        require(!reentrancyLock, "Reentrancy detected");

```
### [G-06] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contracts/BlurExchange.sol:L53    function _authorizeUpgrade(address) internal override onlyOwner {}

```


### [G-07] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
contracts/ExecutionDelegate.sol:L36    function approveContract(address _contract) onlyOwner external {

contracts/ExecutionDelegate.sol:L45    function denyContract(address _contract) onlyOwner external {

contracts/PolicyManager.sol:L25    function addPolicy(address policy) external override onlyOwner {

contracts/PolicyManager.sol:L36    function removePolicy(address policy) external override onlyOwner {

contracts/BlurExchange.sol:L43    function open() external onlyOwner {

contracts/BlurExchange.sol:L47    function close() external onlyOwner {

contracts/BlurExchange.sol:L53    function _authorizeUpgrade(address) internal override onlyOwner {}

```

### [G-08] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
contracts/BlurExchange.sol:L479            totalFee += fee;

```

### [G-09] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per loop.


#### Findings:
```
contracts/PolicyManager.sol:L77        for (uint256 i = 0; i < length; i++) {

contracts/BlurExchange.sol:L199        for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol:L476        for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol:L38        for (uint256 i = 0; i < proof.length; i++) {

contracts/lib/EIP712.sol:L77        for (uint256 i = 0; i < fees.length; i++) {

```

### [G-10] Using private rather than public for constants to saves gas.


#### Impact
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.


#### Findings:
```
contracts/BlurExchange.sol:L57    string public constant name = "Blur Exchange";

contracts/BlurExchange.sol:L58    string public constant version = "1.0";

contracts/BlurExchange.sol:L59    uint256 public constant INVERSE_BASIS_POINT = 10000;

```

### [G-11] Revert message greater than 32 bytes


#### Impact
Keep revert message lower than or equal to 32 bytes to save gas.


#### Findings:
```
contracts/ExecutionDelegate.sol:L22        require(contracts[msg.sender], "Contract is not approved to make transfers");

contracts/BlurExchange.sol:L482        require(totalFee <= price, "Total amount of fees are more than the price");

```

### [G-12] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
contracts/PolicyManager.sol:L77        for (uint256 i = 0; i < length; i++) {

contracts/BlurExchange.sol:L199        for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol:L475        uint256 totalFee = 0;

contracts/BlurExchange.sol:L476        for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol:L38        for (uint256 i = 0; i < proof.length; i++) {

contracts/lib/EIP712.sol:L77        for (uint256 i = 0; i < fees.length; i++) {

```

### [G-13] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contracts/PolicyManager.sol:L77        for (uint256 i = 0; i < length; i++) {

contracts/BlurExchange.sol:L199        for (uint8 i = 0; i < orders.length; i++) {

contracts/BlurExchange.sol:L476        for (uint8 i = 0; i < fees.length; i++) {

contracts/lib/MerkleVerifier.sol:L38        for (uint256 i = 0; i < proof.length; i++) {

contracts/lib/EIP712.sol:L77        for (uint256 i = 0; i < fees.length; i++) {

```




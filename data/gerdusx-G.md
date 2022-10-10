 ## Gas Optimizations
 ### [G-1] Short require/revert strings save gas
 Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas 

 There is 1 occurrence:
```
 BlurExchange.sol:482	         require(totalFee <= price, "Total amount of fees are more than the price");
```
 ### [G-2] Use prefix not postfix in loops
 Using a pre-increment (++i) instead of a post-increment (i++) saves gas for each loop cycle and so can have a big gas impact when the loop executes on a large number of elements. Same for --i vs i--

Post-increments (or post-decrements) return the old value before incrementing or decrementing, where pre-increments (or pre-decrements) return the new value 

 There are 5 occurrences:
```
 BlurExchange.sol:199	         for (uint8 i = 0; i < orders.length; i++) {
 BlurExchange.sol:476	         for (uint8 i = 0; i < fees.length; i++) {
 EIP712.sol:77	         for (uint256 i = 0; i < fees.length; i++) {
 MerkleVerifier.sol:38	         for (uint256 i = 0; i < proof.length; i++) {
 PolicyManager.sol:77	         for (uint256 i = 0; i < length; i++) {
```
 ### [G-3] Redundant zero initialization
 Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas. 

 There are 6 occurrences:
```
 BlurExchange.sol:199	         for (uint8 i = 0; i < orders.length; i++) {
 BlurExchange.sol:476	         for (uint8 i = 0; i < fees.length; i++) {
 EIP712.sol:77	         for (uint256 i = 0; i < fees.length; i++) {
 MerkleVerifier.sol:38	         for (uint256 i = 0; i < proof.length; i++) {
 PolicyManager.sol:77	         for (uint256 i = 0; i < length; i++) {
 BlurExchange.sol:475	         uint256 totalFee = 0;
```
 ### [G-4] Increments/decrements can be unchecked in for-loops
 In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. 

 There are 5 occurrences:
```
 BlurExchange.sol:199	         for (uint8 i = 0; i < orders.length; i++) {
 BlurExchange.sol:476	         for (uint8 i = 0; i < fees.length; i++) {
 EIP712.sol:77	         for (uint256 i = 0; i < fees.length; i++) {
 MerkleVerifier.sol:38	         for (uint256 i = 0; i < proof.length; i++) {
 PolicyManager.sol:77	         for (uint256 i = 0; i < length; i++) {
```
 ### [G-5] Use Custom Errors instead of revert()/require()
 Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas 

 There are 20 occurrences:
```
 BlurExchange.sol:139	         require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
 BlurExchange.sol:140	         require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
 BlurExchange.sol:142	         require(_validateSignatures(sell, sellHash), "Sell failed authorization");
 BlurExchange.sol:143	         require(_validateSignatures(buy, buyHash), "Buy failed authorization");
 BlurExchange.sol:219	         require(address(_executionDelegate) != address(0), "Address cannot be zero");
 BlurExchange.sol:228	         require(address(_policyManager) != address(0), "Address cannot be zero");
 BlurExchange.sol:237	         require(_oracle != address(0), "Address cannot be zero");
 BlurExchange.sol:318	             require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
 BlurExchange.sol:407	         require(v == 27 || v == 28, "Invalid v parameter");
 BlurExchange.sol:424	             require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
 BlurExchange.sol:428	             require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
 BlurExchange.sol:431	         require(canMatch, "Orders cannot be matched");
 BlurExchange.sol:482	         require(totalFee <= price, "Total amount of fees are more than the price");
 BlurExchange.sol:534	         require(_exists(collection), "Collection does not exist");
 ExecutionDelegate.sol:77	         require(revokedApproval[from] == false, "User has revoked approval");
 ExecutionDelegate.sol:92	         require(revokedApproval[from] == false, "User has revoked approval");
 ExecutionDelegate.sol:108	         require(revokedApproval[from] == false, "User has revoked approval");
 ExecutionDelegate.sol:124	         require(revokedApproval[from] == false, "User has revoked approval");
 PolicyManager.sol:26	         require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
 PolicyManager.sol:37	         require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```
 ### [G-6] Array length should not be looked up in every loop of a for-loop
 Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop. 

 There are 4 occurrences:
```
 BlurExchange.sol:199	         for (uint8 i = 0; i < orders.length; i++) {
 BlurExchange.sol:476	         for (uint8 i = 0; i < fees.length; i++) {
 EIP712.sol:77	         for (uint256 i = 0; i < fees.length; i++) {
 MerkleVerifier.sol:38	         for (uint256 i = 0; i < proof.length; i++) {
```
 ### [G-7] Using bools for storage incurs overhead
 ```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past 

 There are 4 occurrences:
```
 BlurExchange.sol:71	     mapping(bytes32 => bool) public cancelledOrFilled;
 ExecutionDelegate.sol:18	     mapping(address => bool) public contracts;
 ExecutionDelegate.sol:19	     mapping(address => bool) public revokedApproval;
 ReentrancyGuarded.sol:10	     bool reentrancyLock = false;
```
 ### [G-8] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
 When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. 

 There is 1 occurrence:
```
 BlurExchange.sol:383	         uint8 v; bytes32 r; bytes32 s;
```
 ### [G-9] Using private rather than public for constants, saves gas
 If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table 

 There are 7 occurrences:
```
 BlurExchange.sol:57	     string public constant name = "Blur Exchange";
 BlurExchange.sol:58	     string public constant version = "1.0";
 BlurExchange.sol:59	     uint256 public constant INVERSE_BASIS_POINT = 10000;
 EIP712.sol:20	     bytes32 constant public FEE_TYPEHASH = keccak256(
 EIP712.sol:23	     bytes32 constant public ORDER_TYPEHASH = keccak256(
 EIP712.sol:26	     bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
 EIP712.sol:29	     bytes32 constant public ROOT_TYPEHASH = keccak256(
```
 ### [G-10] Duplicated require()/revert() checks should be refactored to a modifier or function
 Saves deployment costs 

 There is 1 occurrence:
```
 ExecutionDelegate.sol:92	         require(revokedApproval[from] == false, "User has revoked approval");
```
 ### [G-11] Empty blocks should be removed or emit something
 The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

 There is 1 occurrence:
```
 BlurExchange.sol:53	     function _authorizeUpgrade(address) internal override onlyOwner {}
```
 ### [G-12] Functions guaranteed to revert when called by normal users can be marked payable
 If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 

 There are 11 occurrences:
```
 BlurExchange.sol:43	     function open() external onlyOwner {
 BlurExchange.sol:47	     function close() external onlyOwner {
 BlurExchange.sol:53	     function _authorizeUpgrade(address) internal override onlyOwner {}
 BlurExchange.sol:215	     function setExecutionDelegate(IExecutionDelegate _executionDelegate)
 BlurExchange.sol:224	     function setPolicyManager(IPolicyManager _policyManager)
 BlurExchange.sol:233	     function setOracle(address _oracle)
 BlurExchange.sol:242	     function setBlockRange(uint256 _blockRange)
 ExecutionDelegate.sol:36	     function approveContract(address _contract) onlyOwner external {
 ExecutionDelegate.sol:45	     function denyContract(address _contract) onlyOwner external {
 PolicyManager.sol:25	     function addPolicy(address policy) external override onlyOwner {
 PolicyManager.sol:36	     function removePolicy(address policy) external override onlyOwner {
```
 ### [G-13] Use bytes32 instead of string
 Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming than bytes32. 

 There are 2 occurrences:
```
 BlurExchange.sol:57	     string public constant name = "Blur Exchange";
 BlurExchange.sol:58	     string public constant version = "1.0";
```

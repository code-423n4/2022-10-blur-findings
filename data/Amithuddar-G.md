1)++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP

File: 2022-10-blur\contracts\BlurExchange.sol
  199,46:         for (uint8 i = 0; i < orders.length; i++) {
  476,44:         for (uint8 i = 0; i < fees.length; i++) {
  
File: 2022-10-blur\contracts\PolicyManager.sol
  77,41:         for (uint256 i = 0; i < length; i++) {

File: 2022-10-blur\contracts\lib\EIP712.sol
  77,46:         for (uint256 i = 0; i < fees.length; i++) {

File: 2022-10-blur\contracts\lib\MerkleVerifier.sol
  38,47:         for (uint256 i = 0; i < proof.length; i++) { 

2)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  

File: 2022-10-blur\contracts\BlurExchange.sol
  199,46:         for (uint8 i = 0; i < orders.length; i++) {
  476,44:         for (uint8 i = 0; i < fees.length; i++) {
  
File: 2022-10-blur\contracts\PolicyManager.sol
  77,41:         for (uint256 i = 0; i < length; i++) {

File: 2022-10-blur\contracts\lib\EIP712.sol
  77,46:         for (uint256 i = 0; i < fees.length; i++) {

File: 2022-10-blur\contracts\lib\MerkleVerifier.sol
  38,47:         for (uint256 i = 0; i < proof.length; i++) { 

3) ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops   
   
File: 2022-10-blur\contracts\BlurExchange.sol
  199,46:         for (uint8 i = 0; i < orders.length; i++) {
  476,44:         for (uint8 i = 0; i < fees.length; i++) {
  
File: 2022-10-blur\contracts\PolicyManager.sol
  77,41:         for (uint256 i = 0; i < length; i++) {

File: 2022-10-blur\contracts\lib\EIP712.sol
  77,46:         for (uint256 i = 0; i < fees.length; i++) {

File: 2022-10-blur\contracts\lib\MerkleVerifier.sol
  38,47:         for (uint256 i = 0; i < proof.length; i++) { 

4)<ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, 

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

File: 2022-10-blur\contracts\BlurExchange.sol
  199,46:         for (uint8 i = 0; i < orders.length; i++) {
  476,44:         for (uint8 i = 0; i < fees.length; i++) {

File: 2022-10-blur\contracts\lib\EIP712.sol
  77,46:         for (uint256 i = 0; i < fees.length; i++) {

File: 2022-10-blur\contracts\lib\MerkleVerifier.sol
  38,47:         for (uint256 i = 0; i < proof.length; i++) { 

5)X = X + Y IS CHEAPER THAN X += Y

File: 2022-10-blur\contracts\BlurExchange.sol
  208,28:         nonces[msg.sender] += 1;
  479,22:             totalFee += fee;      

6)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table
  
   
File: 2022-10-blur\contracts\BlurExchange.sol
  57,12:     string public constant name = "Blur Exchange";
  58,12:     string public constant version = "1.0";
  59,13:     uint256 public constant INVERSE_BASIS_POINT = 10000; 
  
7)Use custom errors rather than revert()/require() strings to save gas 


File: 2022-10-blur\contracts\BlurExchange.sol
  36,9:         require(isOpen == 1, "Closed");
  139,9:         require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
  140,9:         require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
  142,9:         require(_validateSignatures(sell, sellHash), "Sell failed authorization");
  143,9:         require(_validateSignatures(buy, buyHash), "Buy failed authorization");
  219,9:         require(address(_executionDelegate) != address(0), "Address cannot be zero");
  228,9:         require(address(_policyManager) != address(0), "Address cannot be zero");
  237,9:         require(_oracle != address(0), "Address cannot be zero");
  318,13:             require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
  407,9:         require(v == 27 || v == 28, "Invalid v parameter");
  424,13:             require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
  428,13:             require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
  431,9:         require(canMatch, "Orders cannot be matched");
  482,9:         require(totalFee <= price, "Total amount of fees are more than the price");
  534,9:         require(_exists(collection), "Collection does not exist");

File: 2022-10-blur\contracts\ExecutionDelegate.sol
  22,9:         require(contracts[msg.sender], "Contract is not approved to make transfers");
  77,9:         require(revokedApproval[from] == false, "User has revoked approval");
  92,9:         require(revokedApproval[from] == false, "User has revoked approval");
  108,9:         require(revokedApproval[from] == false, "User has revoked approval");
  124,9:         require(revokedApproval[from] == false, "User has revoked approval");

File: 2022-10-blur\contracts\PolicyManager.sol
  26,9:         require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
  37,9:         require(_whitelistedPolicies.contains(policy), "Not whitelisted");

File: 2022-10-blur\contracts\lib\ReentrancyGuarded.sol
  14,9:         require(!reentrancyLock, "Reentrancy detected");  
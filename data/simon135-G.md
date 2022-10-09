## make mappings into structs 
```
ExecutionDelegate.sol:17:    mapping(address => bool) public contracts;
ExecutionDelegate.sol:18:    mapping(address => bool) public revokedApproval;
```
## dont use bool they incur overhead
```
ExecutionDelegate.sol:17:    mapping(address => bool) public contracts;
ExecutionDelegate.sol:18:    mapping(address => bool) public revokedApproval;
```
## make i++ into ++i 
```
lib/EIP712.sol:62:        for (uint256 i = 0; i < fees.length; i++) {
lib/MerkleVerifier.sol:39:        for (uint256 i = 0; i < proof.length; i++) {
PolicyManager.sol:88:        for (uint256 i = 0; i < length; i++) {
BlurExchange.sol:212:        for (uint8 i = 0; i < orders.length; i++) {
BlurExchange.sol:521:        for (uint8 i = 0; i < fees.length; i++) {
```
## make require into error statements to save gas
```
lib/ReentrancyGuarded.sol:14:        require(!reentrancyLock, "Reentrancy detected");
PolicyManager.sol:27:        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
PolicyManager.sol:38:        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
BlurExchange.sol:34:        require(isOpen == 1, "Closed");
BlurExchange.sol:131:        require(sell.order.side == Side.Sell);
BlurExchange.sol:135:        require(
BlurExchange.sol:139:        require(
BlurExchange.sol:144:        require(
BlurExchange.sol:148:        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
BlurExchange.sol:196:        require(msg.sender == order.trader);
BlurExchange.sol:231:        require(
BlurExchange.sol:243:        require(
BlurExchange.sol:252:        require(_oracle != address(0), "Address cannot be zero");
BlurExchange.sol:328:            require(
BlurExchange.sol:437:        require(v == 27 || v == 28, "Invalid v parameter");
BlurExchange.sol:459:            require(
BlurExchange.sol:468:            require(
BlurExchange.sol:476:        require(canMatch, "Orders cannot be matched");
BlurExchange.sol:497:            require(msg.value == price);
BlurExchange.sol:529:        require(
BlurExchange.sol:584:        require(_exists(collection), "Collection does not exist");
ExecutionDelegate.sol:21:        require(
ExecutionDelegate.sol:83:        require(revokedApproval[from] == false, "User has revoked approval");
ExecutionDelegate.sol:101:        require(revokedApproval[from] == false, "User has revoked approval");
ExecutionDelegate.sol:120:        require(revokedApproval[from] == false, "User has revoked approval");
ExecutionDelegate.sol:137:        require(revokedApproval[from] == false, "User has revoked approval");
```
## make i into uncheched to save gas 
```
lib/EIP712.sol:62:        for (uint256 i = 0; i < fees.length; i++) {
lib/MerkleVerifier.sol:39:        for (uint256 i = 0; i < proof.length; i++) {
PolicyManager.sol:88:        for (uint256 i = 0; i < length; i++) {
BlurExchange.sol:212:        for (uint8 i = 0; i < orders.length; i++) {
BlurExchange.sol:521:        for (uint8 i = 0; i < fees.length; i++) {
```
## there can be a dos if to many policys are added 
```
        for (uint256 i = 0; i < length; i++) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        }
```
add a max and min to how many can be added
## you  can take away `==false` it consumes more gas 
atleast 3 gas more 
instead make it `revoekApproval[from]`
```
ExecutionDelegate.sol:83:        require(revokedApproval[from] == false, "User has revoked approval");
ExecutionDelegate.sol:101:        require(revokedApproval[from] == false, "User has revoked approval");
ExecutionDelegate.sol:120:        require(revokedApproval[from] == false, "User has revoked approval");
ExecutionDelegate.sol:137:        require(revokedApproval[from] == false, "User has revoked approval");
```
## cache array.length into memory variable to save gas 
```
lib/EIP712.sol:62:        for (uint256 i = 0; i < fees.length; i++) {
lib/MerkleVerifier.sol:39:        for (uint256 i = 0; i < proof.length; i++) {
PolicyManager.sol:88:        for (uint256 i = 0; i < length; i++) {
BlurExchange.sol:212:        for (uint8 i = 0; i < orders.length; i++) {
BlurExchange.sol:521:        for (uint8 i = 0; i < fees.length; i++) {
```
##  make address(0) into long form to save gas & USE ASSEMBLY TO CHECK FOR ADDRESS(0)
```
BlurExchange.sol:232:            address(_executionDelegate) != address(0),
BlurExchange.sol:244:            address(_policyManager) != address(0),
BlurExchange.sol:252:        require(_oracle != address(0), "Address cannot be zero");
BlurExchange.sol:275:        (order.trader != address(0)) &&
BlurExchange.sol:496:        if (paymentToken == address(0)) {
BlurExchange.sol:556:        if (paymentToken == address(0)) {
```
## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
```
PolicyManager.sol:26:    function addPolicy(address policy) external override onlyOwner {
PolicyManager.sol:37:    function removePolicy(address policy) external override onlyOwner {
BlurExchange.sol:41:    function open() external onlyOwner {
BlurExchange.sol:46:    function close() external onlyOwner {
BlurExchange.sol:52:    function _authorizeUpgrade(address) internal override onlyOwner {}
BlurExchange.sol:229:        onlyOwner
BlurExchange.sol:241:        onlyOwner
BlurExchange.sol:251:    function setOracle(address _oracle) external onlyOwner {
BlurExchange.sol:257:    function setBlockRange(uint256 _blockRange) external onlyOwner {
ExecutionDelegate.sol:39:    function approveContract(address _contract) external onlyOwner {
ExecutionDelegate.sol:48:    function denyContract(address _contract) external onlyOwner {
```
##  Don't Initialize Variables with Default Value
*There are 7 instances of this issue*
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;
```

&nbsp;
&nbsp;


##  CACHE THE LENGTH OF ARRAYS IN LOOPS
*There are 5  instances of this issue*
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

&nbsp;
&nbsp;

##  `++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT’S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)
*There are 5  instances of this issue*
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

&nbsp;
&nbsp;

## USING UNCHECKED BLOCKS TO SAVE GAS - INCREMENTS IN FOR LOOP CAN BE UNCHECKED 
*There are 5  instances of this issue*

```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

&nbsp;
&nbsp;

##  `<X> += <Y>`COSTS MORE GAS THAN `<X> = <X> + <Y>` FOR STATE VARIABLES

*There are 2  instances of this issue*
```solidity
contracts/BlurExchange.sol::208 =>        nonces[msg.sender] += 1;
contracts/BlurExchange.sol::479 =>        totalFee += fee;
```



&nbsp;
&nbsp;




##  USING BOOLS FOR STORAGE INCURS OVERHEAD
*There are 3 instances of this issue*
```solidity
contracts/BlurExchange.sol::71 =>    mapping(bytes32 => bool) public cancelledOrFilled;
contracts/ExecutionDelegate.sol::18 =>    mapping(address => bool) public contracts;
contracts/ExecutionDelegate.sol::19 =>    mapping(address => bool) public revokedApproval;
```


&nbsp;
&nbsp;


## MULTIPLE `ADDRESS` MAPPINGS CAN BE COMBINED INTO A SINGLE `MAPPING` OF AN `ADDRESS` TO A `STRUCT`, WHERE APPROPRIATE
*There are 3 instances of this issue*
```solidity
contracts/BlurExchange.sol::72 =>    mapping(address => uint256) public nonces;
contracts/ExecutionDelegate.sol::18 =>    mapping(address => bool) public contracts;
contracts/ExecutionDelegate.sol::19 =>    mapping(address => bool) public revokedApproval;
```


&nbsp;
&nbsp;


## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED `PAYABLE`
*There are 11 instances of this issue*
```solidity
contracts/BlurExchange.sol::43 =>        function open() external onlyOwner {
contracts/BlurExchange.sol::47 =>        function close() external onlyOwner {
contracts/BlurExchange.sol::53 =>        function _authorizeUpgrade(address) internal override onlyOwner {}
contracts/BlurExchange.sol::215-218 =>            function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
contracts/BlurExchange.sol::225-227 =>                function setPolicyManager(IPolicyManager _policyManager)
        external
        onlyOwner
    {
contracts/BlurExchange.sol::233-236=>                function setOracle(address _oracle)
        external
        onlyOwner
    {
contracts/BlurExchange.sol::242-245 =>                function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {

contracts/ExecutionDelegate.sol::36 =>        function approveContract(address _contract) onlyOwner external {
contracts/ExecutionDelegate.sol::45 =>        function denyContract(address _contract) onlyOwner external {

contracts/PolicyManagere.sol::25 =>        function addPolicy(address policy) external override onlyOwner {
contracts/PolicyManagere.sol::36 =>    function removePolicy(address policy) external override onlyOwner {
```


&nbsp;
&nbsp;

## USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

*There are 3  instances of this issue*

```solidity
contracts/BlurExchange.sol::57 =>        string public constant name = "Blur Exchange";
contracts/BlurExchange.sol::58 =>        string public constant version = "1.0";
contracts/BlurExchange.sol::59 =>        uint256 public constant INVERSE_BASIS_POINT = 10000;
```


## USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS

*There are 22 instances of this issue*
```solidity
contracts/BlurExchange.sol::36=>        require(isOpen == 1, "Closed");
contracts/BlurExchange.sol::139 =>        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
contracts/BlurExchange.sol::140 =>        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
contracts/BlurExchange.sol::142 =>        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
contracts/BlurExchange.sol::143 =>        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
contracts/BlurExchange.sol::219 =>        require(address(_executionDelegate) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::228 =>        require(address(_policyManager) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::237 =>        require(_oracle != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::318 =>        require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
contracts/BlurExchange.sol::407=>        require(v == 27 || v == 28, "Invalid v parameter");
contracts/BlurExchange.sol::424 =>    require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
contracts/BlurExchange.sol::428 =>    require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
contracts/BlurExchange.sol::431 =>    require(canMatch, "Orders cannot be matched");
contracts/BlurExchange.sol::482 =>     require(totalFee <= price, "Total amount of fees are more than the price");
contracts/BlurExchange.sol::534 =>    require(_exists(collection), "Collection does not exist");


contracts/ExecutionDelegate.sol::22 =>  require(contracts[msg.sender], "Contract is not approved to make transfers");
contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::92 =>  require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::108 =>  require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::124 =>  require(revokedApproval[from] == false, "User has revoked approval");

contracts/PolicyManagere.sol::26 =>        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");onlyOwner {
contracts/PolicyManagere.sol::37 =>    require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```
##  Don't Initialize Variables with Default Value
*There are 7 instances of this issue*
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;
```

&nbsp;
&nbsp;


##  CACHE THE LENGTH OF ARRAYS IN LOOPS
*There are 5  instances of this issue*
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

&nbsp;
&nbsp;

##  `++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT’S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)
*There are 5  instances of this issue*
```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

&nbsp;
&nbsp;

## USING UNCHECKED BLOCKS TO SAVE GAS - INCREMENTS IN FOR LOOP CAN BE UNCHECKED 
*There are 5  instances of this issue*

```solidity
contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```

&nbsp;
&nbsp;

##  `<X> += <Y>`COSTS MORE GAS THAN `<X> = <X> + <Y>` FOR STATE VARIABLES

*There are 2  instances of this issue*
```solidity
contracts/BlurExchange.sol::208 =>        nonces[msg.sender] += 1;
contracts/BlurExchange.sol::479 =>        totalFee += fee;
```



&nbsp;
&nbsp;




##  USING BOOLS FOR STORAGE INCURS OVERHEAD
*There are 3 instances of this issue*
```solidity
contracts/BlurExchange.sol::71 =>    mapping(bytes32 => bool) public cancelledOrFilled;
contracts/ExecutionDelegate.sol::18 =>    mapping(address => bool) public contracts;
contracts/ExecutionDelegate.sol::19 =>    mapping(address => bool) public revokedApproval;
```


&nbsp;
&nbsp;


## MULTIPLE `ADDRESS` MAPPINGS CAN BE COMBINED INTO A SINGLE `MAPPING` OF AN `ADDRESS` TO A `STRUCT`, WHERE APPROPRIATE
*There are 3 instances of this issue*
```solidity
contracts/BlurExchange.sol::72 =>    mapping(address => uint256) public nonces;
contracts/ExecutionDelegate.sol::18 =>    mapping(address => bool) public contracts;
contracts/ExecutionDelegate.sol::19 =>    mapping(address => bool) public revokedApproval;
```


&nbsp;
&nbsp;


## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED `PAYABLE`
*There are 11 instances of this issue*
```solidity
contracts/BlurExchange.sol::43 =>        function open() external onlyOwner {
contracts/BlurExchange.sol::47 =>        function close() external onlyOwner {
contracts/BlurExchange.sol::53 =>        function _authorizeUpgrade(address) internal override onlyOwner {}
contracts/BlurExchange.sol::215-218 =>            function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
contracts/BlurExchange.sol::225-227 =>                function setPolicyManager(IPolicyManager _policyManager)
        external
        onlyOwner
    {
contracts/BlurExchange.sol::233-236=>                function setOracle(address _oracle)
        external
        onlyOwner
    {
contracts/BlurExchange.sol::242-245 =>                function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {

contracts/ExecutionDelegate.sol::36 =>        function approveContract(address _contract) onlyOwner external {
contracts/ExecutionDelegate.sol::45 =>        function denyContract(address _contract) onlyOwner external {

contracts/PolicyManager.sol::25 =>        function addPolicy(address policy) external override onlyOwner {
contracts/PolicyManager.sol::36 =>    function removePolicy(address policy) external override onlyOwner {
```


&nbsp;
&nbsp;

## USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

*There are 3  instances of this issue*

```solidity
contracts/BlurExchange.sol::57 =>        string public constant name = "Blur Exchange";
contracts/BlurExchange.sol::58 =>        string public constant version = "1.0";
contracts/BlurExchange.sol::59 =>        uint256 public constant INVERSE_BASIS_POINT = 10000;
```


## USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS

*There are 22 instances of this issue*
```solidity
contracts/BlurExchange.sol::36=>        require(isOpen == 1, "Closed");
contracts/BlurExchange.sol::139 =>        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
contracts/BlurExchange.sol::140 =>        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
contracts/BlurExchange.sol::142 =>        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
contracts/BlurExchange.sol::143 =>        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
contracts/BlurExchange.sol::219 =>        require(address(_executionDelegate) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::228 =>        require(address(_policyManager) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::237 =>        require(_oracle != address(0), "Address cannot be zero");
contracts/BlurExchange.sol::318 =>        require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
contracts/BlurExchange.sol::407=>        require(v == 27 || v == 28, "Invalid v parameter");
contracts/BlurExchange.sol::424 =>    require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
contracts/BlurExchange.sol::428 =>    require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
contracts/BlurExchange.sol::431 =>    require(canMatch, "Orders cannot be matched");
contracts/BlurExchange.sol::482 =>     require(totalFee <= price, "Total amount of fees are more than the price");
contracts/BlurExchange.sol::534 =>    require(_exists(collection), "Collection does not exist");


contracts/ExecutionDelegate.sol::22 =>  require(contracts[msg.sender], "Contract is not approved to make transfers");
contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::92 =>  require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::108 =>  require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol::124 =>  require(revokedApproval[from] == false, "User has revoked approval");

contracts/PolicyManager.sol::26 =>        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");onlyOwner {
contracts/PolicyManager.sol::37 =>    require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

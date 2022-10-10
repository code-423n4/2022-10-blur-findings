1-Use different function names
The name of the function will have an impact on gas consumption because it will change its position in the contract. The order depends on the method Id, so if you rename the frequently accessed function to more early method Id, you can save gas cost ( 22 gas reduced in each transaction). 
Prioritize functions most used.
PolicyManager.sol
addPolicy(address) 
removePolicy(address)
Etc...

2-USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE DEPLOYMENT GAS
PolicyManager.sol
addPolicy(address) 
removePolicy(address)
Etc...

3-Avoid unnecessary event emission
Logging opcodes are costly and gas cost increase when its argument increase.
BlurExchange.sol
Open() 
Close()
event OrderCancelled(bytes32 hash);
event NonceIncremented(address trader, uint256 newNonce);
event NewExecutionDelegate(IExecutionDelegate executionDelegate);
event NewPolicyManager(IPolicyManager policyManager);
event NewOracle(address oracle);
event NewBlockRange(uint256 blockRange);
OrderCancelled

Use read function external return (uint open/ close) { }




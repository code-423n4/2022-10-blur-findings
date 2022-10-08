## In the file ExecutionDelegate.sol: 
Line 18, 19:    mapping(address => bool) public contracts;
    mapping(address => bool) public revokedApproval;
mapping of bool should not be used in order to save gas

Line 22:	        require(contracts[msg.sender], "Contract is not approved to make transfers");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 77:	        require(revokedApproval[from] == false, "User has revoked approval");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 92:	        require(revokedApproval[from] == false, "User has revoked approval");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 108:	        require(revokedApproval[from] == false, "User has revoked approval");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 124:	        require(revokedApproval[from] == false, "User has revoked approval");
the require() function should not be used. revert error() should be used instead in order to save gas

## In the file BlurExchange.sol: 
Line 71: mapping(bytes32 => bool) public cancelledOrFilled;
mapping of bool should not be used in order to save gas

Line 36:	        require(isOpen == 1, "Closed");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 134:	        require(sell.order.side == Side.Sell);
the require() function should not be used. revert error() should be used instead in order to save gas

Line 139:	        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 140:	        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 142:	        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 143:	        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 183:	        require(msg.sender == order.trader);
the require() function should not be used. revert error() should be used instead in order to save gas

Line 219:	        require(address(_executionDelegate) != address(0), "Address cannot be zero");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 228:	        require(address(_policyManager) != address(0), "Address cannot be zero");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 237:	        require(_oracle != address(0), "Address cannot be zero");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 318:	            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 407:	        require(v == 27 || v == 28, "Invalid v parameter");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 424:	            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 428:	            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 431:	        require(canMatch, "Orders cannot be matched");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 452:	            require(msg.value == price);
the require() function should not be used. revert error() should be used instead in order to save gas

Line 482:	        require(totalFee <= price, "Total amount of fees are more than the price");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 534:	        require(_exists(collection), "Collection does not exist");
the require() function should not be used. revert error() should be used instead in order to save gas

## In the file PolicyManager.sol: 
line 47: policy

Line 26:	        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
the require() function should not be used. revert error() should be used instead in order to save gas

Line 37:	        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
the require() function should not be used. revert error() should be used instead in order to save gas
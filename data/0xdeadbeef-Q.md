# low
## contracts/PolicyManager.sol
PolicyManager.addPolicy(address) (contracts/PolicyManager.sol#25-30) ignores return value by _whitelistedPolicies.add(policy) (contracts/PolicyManager.sol#27)
PolicyManager.removePolicy(address) (contracts/PolicyManager.sol#36-41) ignores return value by _whitelistedPolicies.remove(policy) (contracts/PolicyManager.sol#38)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#unused-return

## In the file BlurExchange.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 97: _weth
	line 233: _oracle
	line 445: seller
	line 446: buyer
	line 472: from
	line 472: from
	line 499: to
	line 526: collection
	line 472: from
	line 499: to
	line 548: what
```
## In the file ExecutionDelegate.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 36: _contract
	line 45: _contract
	line 73: collection
	line 88: collection
	line 104: collection
	line 119: token
```
## In the file PolicyManager.sol: 
these address variables are not checked whether they are address(0), address variable should check if it is zero
```
	line 25: policy
	line 36: policy
	line 47: policy
```

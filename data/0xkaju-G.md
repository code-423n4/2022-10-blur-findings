Redundant Check

The addPolicy function checks if the input parameter has already been added to the list or not. EnumerableSet.add() function already performs this check. There is no need to perform this check again.

Path: ./contracts/PolicyManager.sol

Recommendation: Remove old "require" from the function. Implement new check as 
“require(!_whitelistedPolicies. add(policy), "Already whitelisted”)”


## Redundant check
The addPolicy() function checks if the input parameter already added to the list or not. EnumerableSet.add() function already performs this check. There is no need to do this check again.

    function addPolicy(address policy) external override onlyOwner {
        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
        _whitelistedPolicies.add(policy);

        emit PolicyWhitelisted(policy);
    }

There is no need to this require check instead of we can do like this: 

    function addPolicy(address policy) external override onlyOwner {
        require(_whitelistedPolicies.add(policy), "Already whitelisted");
        
        emit PolicyWhitelisted(policy);
    }

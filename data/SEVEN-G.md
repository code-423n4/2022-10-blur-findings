# PolicyManager.viewWhitelistedPolicies() ,PolicyManager.addPolicy() ,PolicyManager.removePolicy() ,PolicyManager.removePolicy() ,_whitelistedPolicies SHOULD GET CACHED
## See @audit tag
###  PolicyManager.viewWhitelistedPolicies() 
```
   if (length > _whitelistedPolicies.length() - cursor) {   //@audit gas: should cache "_whitelistedPolicies" (SLOAD 1)
            length = _whitelistedPolicies.length() - cursor; //@audit gas: should cache "_whitelistedPolicies" (SLOAD 2)
           whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);

```
### PolicyManager.addPolicy() 
```
        require(!_whitelistedPolicies.contains(policy), "Already whitelisted"); //@audit gas: should cache "_whitelistedPolicies" (SLOAD 1)
                  _whitelistedPolicies.add(policy);//@audit gas: should cache "_whitelistedPolicies" (SLOAD 2)
        
```
### PolicyManager.removePolicy()
```
       require(_whitelistedPolicies.contains(policy), "Not whitelisted");//@audit gas: should cache "_whitelistedPolicies" (SLOAD 1)
        _whitelistedPolicies.remove(policy);//@audit gas: should cache "_whitelistedPolicies" (SLOAD 2)
``` 
Fixed by caching _whitelistedPolicies (storage variable used in Loop).




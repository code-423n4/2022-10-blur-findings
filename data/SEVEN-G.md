# PolicyManager.viewWhitelistedPolicies() ,_whitelistedPolicies SHOULD GET CACHED
## See @audit tag
```
   if (length > _whitelistedPolicies.length() - cursor) {   //@audit gas: should cache "_whitelistedPolicies" (SLOAD 1)
            length = _whitelistedPolicies.length() - cursor; //@audit gas: should cache "_whitelistedPolicies" (SLOAD 2)
        }

```
Fixed by caching _whitelistedPolicies (storage variable used in Loop).




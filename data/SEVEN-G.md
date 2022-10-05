## Impact
_whitelistedPolicies is called multiple times inside the function function, and memory variables should be used to see less gas

## Proof of Concept
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26-L27
```        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
        _whitelistedPolicies.add(policy);
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37-L38
```
        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
        _whitelistedPolicies.remove(policy);
```


## Tools Used
vscode
## Recommended Mitigation Steps
function use memory variables
1. Need to check address zero check

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L25

```
require(policy != address(0), "zero policy");
```

2. Missing Storage Gap

Since it was used OZ upgradable for upgradable contract so it better if used :

```
uint256[50] private ______gap;
```
The gap is a workaround to that issue: by leaving a 50-slot gap, we’re able to increase the contract’s storage by that amount (provided we also remove the same slots from the gap) with no clashing issues.

This was source : https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable
same as this case : https://forum.openzeppelin.com/t/what-exactly-is-the-reason-for-uint256-50-private-gap/798


3. Incorrect Event 

This was the code :

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L19
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L29

the better practical for this code was  :

```
 event PolicyAdded(address indexed policy);
```

and 

```
emit PolicyAdded(policy);
```

Since the fn was for addPolicy(), so that was good for readibility and code as well.


4. Missing indexed 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L86 - address trader

5. Missing reason string

Files : 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L183
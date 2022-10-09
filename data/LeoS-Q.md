
## [L-01] Missing check for address(0) when assigning value to address state variable.
Zero address checking is the best practice to prevent the redeployment of the contract.

2 instances:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L97
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L100


Consider adding a modifier or checks at the start of this function to check it.

*This is low risk because any risk can only result of an owner's error in calling an external function. This can easily be prevented.*
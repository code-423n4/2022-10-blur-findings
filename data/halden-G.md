#### G-01: USING BOOLS FOR STORAGE INCURS OVERHEAD
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back.
[source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

File: ReentrancyGuarded.sol [line 10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)
`bool reentrancyLock = false;`

File: ExecutionDelegate.sol [line 18](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L18)
`mapping(address => bool) public contracts;`

File: ExecutionDelegate.sol [line 19](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L19)
`mapping(address => bool) public revokedApproval;`

File: BlurExchange.sol [line 71](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L71)
`mapping(bytes32 => bool) public cancelledOrFilled;`

#### G-02 Cache storage values in memory to minimize SLOADs
The code can be optimized by minimising the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3gas). Storage value should get cached in memory

PolicyManager.sol.viewWhitelistedPolicies: _whitelistedPolicies.length() should be cached - _whitelistedPolicies is a storage array 
File: PolicyManager.sol Line [71](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L71)
`_whitelistedPolicies.length()`

New value of nonces[msg.sender] should be saved in additional memory variable
File: BlurExchange.sol Line [208](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208)
`nonces[msg.sender] += 1;` // Used
`emit NonceIncremented(msg.sender, nonces[msg.sender]);` // Used


#### G-03  Remove additional memory variable
The size parameter can be used instead of variable length
File: PolicyManager.sol Line [69](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L69)
` uint256 length = size;`

#### G-04 ++i costs less gas compared to i++ or i += 1 in for loops (~5 gas per iteration)
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration)

File: PolicyManager.sol Line [77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
File: EIP712.sol [line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
File: MerkleVerifier.sol [line 38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
File: BlurExchange.sol [line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)

#### G-05 Using unchecked blocks to save gas - Increments in for loop can be unchecked
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

File: PolicyManager.sol [line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
File: EIP712.sol [line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
File: MerkleVerifier.sol [line 38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
File: BlurExchange.sol [line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)

#### [G-06] USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

File: PolicyManager.sol line [26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26)
`require(!_whitelistedPolicies.contains(policy), "Already whitelisted");`

File: PolicyManager.sol line [37](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37)
`require(_whitelistedPolicies.contains(policy), "Not whitelisted");`

File: ExecutionDelegate.sol line [22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)
`require(contracts[msg.sender], "Contract is not approved to make transfers");`

File: ExecutionDelegate.sol line [77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77)
`require(revokedApproval[from] == false, "User has revoked approval");`

File: ExecutionDelegate.sol line [92](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92)
`require(revokedApproval[from] == false, "User has revoked approval");`

File: ExecutionDelegate.sol line [108](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108)
`require(revokedApproval[from] == false, "User has revoked approval");`

File: ExecutionDelegate.sol line [124](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124)
` require(revokedApproval[from] == false, "User has revoked approval");`

File: BlurExchange.sol line [36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36)
`require(isOpen == 1, "Closed");`

File: BlurExchange.sol line [139-143](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139-L143)
File: BlurExchange.sol line [219](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219)
`require(address(_executionDelegate) != address(0), "Address cannot be zero");`

File: BlurExchange.sol line [228](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228)
`require(address(_policyManager) != address(0), "Address cannot be zero");`

File: BlurExchange.sol line [237](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237)
`require(_oracle != address(0), "Address cannot be zero");`

File: BlurExchange.sol line [318](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318)
`require(block.number - order.blockNumber < blockRange, "Signed block number out of range");`

File: BlurExchange.sol line [407](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407)
`require(v == 27 || v == 28, "Invalid v parameter");`

File: BlurExchange.sol line [424](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424)
`require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");`

File: BlurExchange.sol line [428](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L428)
`require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");`

File: BlurExchange.sol line [431](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L431)
`require(canMatch, "Orders cannot be matched");`

File: BlurExchange.sol line [482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)
`require(totalFee <= price, "Total amount of fees are more than the price");`

File: BlurExchange.sol line [534](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534)
`require(_exists(collection), "Collection does not exist");`

#### [G-07] Add unchecked {} for subtractions where the operands can not underflow because of a previous
File: PolicyManager.sol line [72](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L72)
`length = _whitelistedPolicies.length() - cursor;`

#### [G-08] X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
File: BlurExchange.sol line [208](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208)
`nonces[msg.sender] += 1;`

#### [G-09] Use memory variable instead storage to minimize SLOADs
File: BlurExchange.sol line [221](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L221)
insted of:
`emit NewExecutionDelegate(executionDelegate);`
use:
`emit NewExecutionDelegate(_executionDelegate);`

File: BlurExchange.sol line [230](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L230)
File: BlurExchange.sol line [239](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L239)
File: BlurExchange.sol line [247](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L247)
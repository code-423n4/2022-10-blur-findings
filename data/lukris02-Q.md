# QA Report for Blur Exchange contest

## Overview
During the audit, 1 low and 7 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | [Upgradeable contract is missing a storage gap](#l-1-upgradeable-contract-is-missing-a-storage-gap) | Low | 1
NC-1 | [Order of Functions](#nc-1-order-of-functions) | Non-Critical | 1
NC-2 | [Order of Layout](#nc-2-order-of-layout) | Non-Critical | 3
NC-3 | [Maximum line length exceeded](#nc-3-maximum-line-length-exceeded) | Non-Critical | 1
NC-4 | [Public functions can be external](#nc-4-public-functions-can-be-external) | Non-Critical | 1
NC-5 | [Scientific notation may be used](#nc-5-scientific-notation-may-be-used) | Non-Critical | 1
NC-6 | [Missing NatSpec](#nc-6-missing-natspec) | Non-Critical | 19
NC-7 | [No error message in ```require```](#nc-7-no-error-message-in-require) | Non-Critical | 3

## Low Risk Findings (1)
### L-1. Upgradeable contract is missing a storage gap
##### Description
According to [openzeppelin docs](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps), it isn’t safe to simply add a state variable because it "shifts down" all of the state variables below in the inheritance chain. This makes the storage layouts incompatible, as explained in [Writing Upgradeable Contracts](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#modifying-your-contracts). 

##### Instances
- [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)

##### Recommendation
Add the state variable named ```__gap```.

## Non-Critical Risk Findings (7)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private

##### Instances
public function between external:
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L181

##### Recommendation
Reorder functions where possible.

#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

##### Instances
modifier before events:
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L21-L30
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L35-L41
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L75-L91

##### Recommendation
Place modifiers after events.

#
### NC-3. Maximum line length exceeded
##### Description
Some lines of code are too long.

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388

##### Recommendation
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.  
Make the lines shorter.

#
### NC-4. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17

##### Recommendation
Make public functions external, where possible.

#
### NC-5. Scientific notation may be used
##### Description
For readability, it is better to use scientific notation.

##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59

##### Recommendation
Replace ```10000``` with ```10e4```.

#
### NC-6. Missing NatSpec
##### Description
NatSpec is missing for 19 functions in 5 contracts.

##### Instances
- all 2 functions in [StandardPolicyERC1155.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC1155.sol)
- all 2 functions in [StandardPolicyERC721.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC721.sol)
- all 7 functions in [EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol)
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L45
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L49

##### Recommendation
Add NatSpec for all functions.

#
### NC-7. No error message in ```require```
##### Instances
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183
- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452

##### Recommendation
Add error messages.
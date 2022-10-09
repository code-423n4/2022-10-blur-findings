## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value. Here are some of the few instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L221
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L230
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L239
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L247

## Sanity Checks
Zero address and zero value checks should be implemented on `initialize()` of `BlurExchange.sol`  to avoid accidental error(s) that could result in non-functional calls associated with it:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L118

## Use `immutable` Instead of `constant` Whose Value is Expressed as a Call to `keccak256()`
Despite not saving any gas, it’s part of the best practices to adopt `immutable` variables associated with expressions for calculated values, and better yet, have them passed into the constructor where possible. `constant` variables, on the other hand, are more appropriately used for literal values written into the code. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20-L35

## `constant` Variables Should be Capitalized
Constants should be named with all capital letters with underscores separating words if need be. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L58

Please visit the following style guide for more details:

https://docs.soliditylang.org/en/v0.8.14/style-guide.html

## Non-assembly Method Available
Automated tools would typically flag a contract using inline-assembly as having high complexity, poor readability and error prone as far as security is concerned. As such, avoid using it where possible. For instance, the following block of codes could be replaced with:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L553-L556

```
uint size = address(what).code.length;
```
## String Concatenation Available
Solidity ^0.8.12 features `string.concat()` that can be used instead of `abi.encodePacked(<str>,<str>)`. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L80
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L117

## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of the upgradeable contract, `BlurExchange.sol`, just in case it would entail some child contracts in the future. This would ensure no shifting down of storage in the inheritance chain:

```
uint256[50] private __gap;
``` 

Typically, storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. Otherwise, the variable in the child contract might be overwritten by the upgraded implementation code whenever new variables are added to it. This could have unintended and vulnerable consequences to the child contracts.

Please visit the bottom part of article below for further details: 

https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable

## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L124
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L24
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L27

## Use of `ecrecover` is Susceptible to Signature Malleability
The built-in EVM pre-compiled `ecrecover` is susceptible to signature malleability due to non-unique v and s (which may not always be in the lower half of the modulo set) values, possibly leading to replay attacks. Despite not exploitable in the current implementation with the adoption of nonces, this could prove a vulnerability when not carefully used elsewhere.

Consider using OpenZeppelin’s ECDSA library which has been time tested in preventing this malleability where possible.

## Missing NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are some of the contracts found to be having inadequate NatSpec:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/interfaces/IMatchingPolicy.sol
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC721.sol
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC1155.sol

## Devoid of System Documentation and Complete Technical Specification
A system’s design specification and supporting documentation should be almost as important as the system’s implementation itself. Users rely on high-level documentation to understand the big picture of how a system works. Without spending time and effort to create palatable documentation, a user’s only resource is the code itself, something the vast majority of users cannot understand. Security assessments depend on a complete technical specification to understand the specifics of how a system works. When a behavior is not specified (or is specified incorrectly), security assessments must base their knowledge in assumptions, leading to less effective review. Maintaining and updating code relies on supporting documentation to know why the system is implemented in a specific way. If code maintainers cannot reference documentation, they must rely on memory or assistance to make high-quality changes. Currently, the only documentation for Growth DeFi is a single README file, as well as code comments.

## Unchecked Return Value for `transferERC20()` Call
In `BlurExchange.sol`, there is a call to `executionDelegate.transferERC20()` that does not check the return value:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L511

It is usually a good practice to add a require statement checking the return value or to use something like `safeTransfer()` unless one is sure the given token reverts in case of a failure.

## Inappropriate Use of Conditional Checks
It is always a good practice to utilize conditional checks correctly and do it as earliest as possible.     

The following line of code in `_transferFees()` should be refactored to:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

```
        require(totalFee < price, "Total amount of fees are more than the price");
```
That way, the following code lines in `_transferTo()` may be removed since `amount == 0` has already been taken of in the above code line inclusive of its proper error handling:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L502-L504

## Use `safeTransferFrom` instead of `transferFrom`
ERC721 has both `safeTransferFrom` and `transferFrom`, where `safeTransferFrom` throws an error if the receiving contract's onERC721Received method doesn't return a specific magic number. This will ensure a receiving contract is capable of receiving the token to prevent a permanent loss. In the light of this, consider disabling `transferERC721Unsafe()` (whose function logic entails `transferFrom`) in `ExecutionDelegate.sol` where possible. 

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L73-L79

## External Call in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L198-L202

Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## Complementary Check for Contract Existence
`_exists()` in `BlurExchange.sol` could at most check the existence of a contract address, serving a great way to shun externally owned accounts (EOA). It cannot, however, tell whether or not it is a matching contract address. Consider adding an optional codehash by having the function refactored to:

```
    function _exists(address what, bytes32 whatCodeHash)
        internal
        view
        returns (bool)
    {
        uint size;
        assembly {
            size := extcodesize(what)
        }
        return (size > 0 && what.codehash == whatCodeHash);
    }
```
Note: `whatCodeHash` will need to be added to the struct `Order`.

## Discrepancy Between Code and Comments
There is a mismatch between what the code implements and what the corresponding comment describes that code implements. For instance, in `BlurExchange.sol`, an array of fees is deducted from seller's price, but the fees aren't paid to anyone. The fees and variable name should be termed as discount/rebate instead. Consider updating the code and/or the comment to be consistent.
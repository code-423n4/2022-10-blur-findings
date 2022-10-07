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
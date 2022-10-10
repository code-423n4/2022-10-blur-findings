## Missing address(0) Check 

address(0) check is a best practice that make sure the input parameter is right as expected. 

### Mitigation 
```js
    if(addr == address(0)) revert AddressZero();
```

### Instance

[BlurExchange::initialize()](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95-L118)

[PolicyManager::addPolicy()](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L25-L30)

## `constants` variable that a result of a call to `keccak256()` should marked `immutable` instead. 

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the `constructor`.

### Instance
[EIP712::{FEE_TYPEHASH, ORDER_TYPEHASH, ORACLE_ORDER_TYPEHASH, ROOT_TYPEHASH, EIP712DOMAIN_TYPEHASH, DOMAIN_SEPARATOR}](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L37)


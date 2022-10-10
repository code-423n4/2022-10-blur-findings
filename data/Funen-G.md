1. Saving gas by deleting declared  `bool`  `reentrancyLock`

in [this](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10) code was declared `reentrancyLock` `bool` was `false`. That code can be declared, like down below :

```
bool reentrancyLock; //@audit saving lot of gas
```

since the default `bool` value was `false` so it can be deleted instead and saving gas. You can read from this too : https://www.oreilly.com/library/view/solidity-programming-essentials/9781788831383/33b99fa0-47c7-4cc4-8f5b-2930580df9a8.xhtml

2. Custom Error 

Files :
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139-L143
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237

3. Short Reason String

Every reason string takes at least 32 bytes. Use short reason strings that fits in 32 bytes or it will become more expensive.

Files : 
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534

4. Removing `= 0`

This implementation code can be saving more gas by removing = 0, it because If a variable was not set/initialized, it is assumed to have default value to 0

Files :
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475



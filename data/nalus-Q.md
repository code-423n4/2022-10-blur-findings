Use bool instead of uin256 since there are only 2 possible values

548: Comments might be missleading this function does not check wheather address exists, but if address is a contract.
279: could be pure, since it doesnt read from memory

1. [Low] Missing zero-address check in the  initiliazer
Missing checks for zero addresses may lead to infunctional protocol, if the variable addresses are set  incorrectly.
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95

2. [QA] Comments might be missleading this function does not check wheather address exists, but if address is a contract 
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L544

3. [QA] For better readability always define size of variables (uint256 instead of uint)
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L96
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L101

4. [QA] When there are 2 possible values use bool instead of uint256
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L33

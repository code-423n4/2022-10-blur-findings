## [G-01] Default value (bool)
## code snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10
## recommendation
if variable is not set/initialized, assumed to have default values ​​(0 for uint, false for bool, address(0) for address...). Initializing it explicitly with its default value is anti-pattern and a waste of gas (about 3 gas per instance). we recommend removing unnecessary explicit code.

## [G-02] Cache _whitelistedPolicies.length
## code snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L71
## recommendation
caching the array length can reduce gas it caused access to a local variable is more cheap than query storage / calldata / memory in solidity. we recommend to cache the  _whitelistedPolicies.length.


## [G-03] Looping
## code snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38
## recommendation
Pre-increments and pre-decrements are cheaper than post increment and post decrement about 5 gas per iteration. we reccomend to use pre increment /decrement.
if variable is not set/initialized, assumed to have default values ​​(0 for uint, false for bool, address(0) for address...). Initializing it explicitly with its default value is anti-pattern and a waste of gas (about 3 gas per instance). we recommend removing unnecessary explicit code for uint.
caching the array length can reduce gas it caused access to a local variable is more cheap than query storage / calldata / memory in solidity. we recommend to cache the array.length.

## [G-04] Change to private
### code  snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L29
## recommendation
In Solidity, unlike other programming languages, it is better to call a single multipurpose function with many parameters and get the requested result back, rather than making different calls for each data. Calling public functions is more expensive than calling internal functions because in the former case all parameters are copied to Memory. Whenever possible, prefer internal function calls, where parameters are passed as references.. we recommend change visibility from public to private or internal can save the gas.

## [G-05] Increment
## code snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208
## recommendation
Pre-increments and pre-decrements are cheaper than post increment and post decrement about 5 gas per iteration. we reccomend to use pre increment /decrement.
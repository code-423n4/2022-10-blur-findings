# 1. Use revert with custom errors instead require. 
This save a lot of gas.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139-L143
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L428
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L431
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534

# 2. Use unchecked block

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

    for (uint256 i = 0; i < length;) {
        whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        unchecked {
            ++i;
        }
    }

# 3. Can cache result of `_whitelistedPolicies.length()`
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L71-L72
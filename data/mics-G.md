* [GAS REPORT](#gas-report)
        * [Caching array size](#caching-array-size)
        * [If the function is onlyOwner you may make it payable to reduce gas usage.](#if-the-function-is-onlyowner-you-may-make-it-payable-to-reduce-gas-usage)

# GAS REPORT

## Caching array size
In the following for loops consider caching the array size instead of loading it every iteration.

### Code Instances:
- [BlurExchange.sol#L475](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L475)
- [BlurExchange.sol#L198](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L198)

## If the function is onlyOwner you may make it payable to reduce gas usage.


### Code Instances:
- [BlurExchange.sol#L236](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L236)
- [BlurExchange.sol#L47](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L47)
- [BlurExchange.sol#L43](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L43)
- [BlurExchange.sol#L218](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L218)

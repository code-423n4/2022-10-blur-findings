## Loop can be cheaper

Loop is an operation that cost a lot of gas. we can make it cheaper by this steps below:
1. Avoid passing 0 value to index parameter(*i*) (-3 gas for MSTORE)
2. Avoid using `array.length` for parameter, reading `memory` is cheaper than operate `array.length`, pass it to `memory` variable instead read it for every loop.
3. Using `++i` instead of `i++`
4. Using `unchecked` math for `++i`

example useage:
```js
    //existing loop by Blur Exchange
    for (uint8 i = 0; i < orders.length; i++) {
        cancelOrder(orders[i]);
    }

    //Cheaper Loop version

    uint length = orders.length;
    for (uint8 i; i < length) {
        cancelOrder(orders[i]);
        unchecked {
            ++i;
        }
    }
```

Instance:
1. BlurExchange.sol 
    * [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
    * [BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

2. PolicyManager.sol
    * [PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)

3. EIP712.sol
    * [EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

4. MerkleVerifier.sol
    * [MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

## Use custom errors rather than revert()/require() strings to save gas
using custom errors can save [~50 gas](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746). Custom error introduced in solidity 0.8.4 so the contract definitely can access this feature. it's cheaper to deploy because it doesnt need to alocate storage to store revert string. 

Instance 
1. BlurExchange.sol
    * [BlurExchange.sol#L36](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L36)
    * [BlurExchange.sol#L139-L143](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139-L143)
    * [BlurExchange.sol#L219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219)
    * [BlurExchange.sol#L228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228)
    * [BlurExchange.sol#L237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)
    * [BlurExchange.sol#L318](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
    * [BlurExchange.sol#L424](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424)
    * [BlurExchange.sol#L428](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428)
    * [BlurExchange.sol#L534](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534)

2. ExecutionDelegate.sol
    * [ExecutionDelegate.sol#L22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
    * [ExecutionDelegate.sol#L77)](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
    * [ExecutionDelegate.sol#L92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
    * [ExecutionDelegate.sol#L108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
    * [ExecutionDelegate.sol#L124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)

3. PolicyManager.sol
    * [PolicyManager.sol#L26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26)
    * [PolicyManager.sol#L37](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)

4. ReentrancyGuarded.sol
    * [ReentrancyGuarded.sol#L14](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L14)

## Unused Import 

Importing contract that don't have useage in the contract can make deploying cost more expensive without an impact for the contract. I recommended that the import should be deleted. 

Instance 
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L8



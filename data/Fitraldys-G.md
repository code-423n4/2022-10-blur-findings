1. use `abi.encodepacked` for gas optimization

Changing the `abi.encode` function to `abi.encodePacked` can save gas since the `abi.encode` function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.

POC :

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L45
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L61
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L91
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L132
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L147

2. Cheaper foor loops

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

```
for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
// Do the thing
// Unchecked pre-increment is cheapest
unchecked { ++i; }
}     
```

POC :

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

3. `require()`/`revert()` strings longer than 32 bytes cost extra gas

Each extra chunk of bytes past the original 32 which costs 3 gas.

resource : https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings

POC :

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L140
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L142
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L143
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L428
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L431
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37

4.  Declaring `uint256 i` can save gas

Declaring `uint256 i = 0;` means doing an MSTORE of the value 0 Instead you could just declare `uint256 i` to declare the variable without assigning it’s default value, saving 3 gas per declaration

POC :

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

5. Useage of uint / int smaller than 32 bytes incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

POC :

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L347

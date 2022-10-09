## `<ARRAY>.LENGTH` Should not be looked up in every loop of a `FOR-LOOP`
Reading array length at each iteration of the loop consumes more gas than necessary.

in the best case scenario (Length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. in the worst case scenario (external calls at each iteration), amount of gas wasted can be massive.

i suggest to change the line code below :
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77


## For loops can be optimized in several ways
To optimize this loop and make it consume less gas, we can do the foloowing things:

1. If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas. 

2. Use ++i instead of i++, which is a cheaper operation (in this case there is no difference between i++ and ++i because we dont use the return value of this expression, which is the only difference between these two expression).

As an example: 
``` 
for (uint256 i = 0; i < variable; i++) { 
```
should be replaced with 
```
for (uint256 i; i < variable; ++i) {
```
I suggest to change the code here:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476


## Use `private` rather than `public` for constants could saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple valuesl there can be a single getter function that returns a tuple of the values of all currently-public constans. Saves gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table.
There is 3 instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59

## Use custom errors rather than `revert()` / `require()` strings to save gas
custom errors are available from solidity version 0.8.4. Custom errors could save about 50 gas each time they are hit by avoiding having to allocate and store the revert string. Not defining the strings could also save the deployment gas.
Proof : https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746

there are 17 instances of this issue:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
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
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534


## no message error in revert `require()`
there is no error message in revert below. I suggest to change the line code here :
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452


## `<x> += <y>` Costs more gas than `<x> = <x> +<y>`
Using the addition operator instead of plus-equals saves gas. i recommend to change the line code below:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479

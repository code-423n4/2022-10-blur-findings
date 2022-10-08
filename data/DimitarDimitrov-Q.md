# 1. Add check to be sure that _contract is address of contract using assembly
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36-L39

# 2. Incorrect order of contract's component
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L33-L91

# 3. Good practice is to use open() not to directly set value to variable.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L104
In first case event Open will not emit.


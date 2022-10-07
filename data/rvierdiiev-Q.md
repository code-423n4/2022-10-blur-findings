1.	Use external instead of public.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L102
2.	Check address params for zero
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L113-L116
3.	Do not use conditions like this `if (condition == false)`. Invert it like this `if (!condition)`.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L267
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
4.	Variable `merklePath` is not used.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388
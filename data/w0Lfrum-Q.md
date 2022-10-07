# QA Report

### Summary

The overall code is well-commented. Unit tests are provided. The logic is split into the corresponding files. The logic is clear when referring to the docs/information given in the code4rena contest page.
There can be improvements in the code by eliminating redundant lines of code which have no affect on the overall logic or state. For example, removing the lines of code which specify "pragma abicoder v2", which is not required as abicoder v2 is set by default in recent compiler versions.

### 1. Specifying pragma abicoder v2 is not required:

Starting from Solidity version 0.8.0, `abicoder v2` is activated by default.  Hence it not required to explicitly use `pragma abicoder v2;`
The following lines of code may be removed.

ExecutionDelegate.sol[#L3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L3)

BlurExchange.sol[#L3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L3)
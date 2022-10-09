# QA Report

### Summary

The overall code is well-commented. Unit tests are provided. The logic is split into the corresponding files. The logic is clear when referring to the docs/information given in the code4rena contest page.
There can be improvements in the code by eliminating redundant lines of code which have no effect on the overall logic or state. For example, removing the lines of code which specify "pragma abicoder v2", which is not required as abicoder v2 is set by default in recent compiler versions.
However, the overall contract size of BlurExchange.sol file exceeds 24576 bytes size limit set in the Spurious Dragon hard fork. 
Hence it is recommended to use libraries, remove some of the string error messages in `require` statements and eliminate any unused local variables that may be declared, but unused in the contract. 

### 1. Specifying pragma abicoder v2 is not required:

Starting from Solidity version 0.8.0, `abicoder v2` is activated by default.  Hence it not required to explicitly use `pragma abicoder v2;`
The following lines of code may be removed.

ExecutionDelegate.sol[#L3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L3)

BlurExchange.sol[#L3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L3)

### 2. Unused local variable in BlurExchange.sol

Variable merklePath is never used within the _validateOracleAuthorization function. 

BlurExchange.sol[#L388](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L388)

Replace `(bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));`
with
`(, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));`
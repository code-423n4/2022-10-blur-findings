## contracts/BlurExchange.sol

1. unused variable

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L388

```solidity
(bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

`bytes32[] memory merklePath` is unused, just remove it works as expected.

2. Stack too deep when compiling inline assembly: Variable value0 is 2 slot(s) too deep inside the stack.

I recommend split the contract file into small contracts to avoid this issue.

3. redundant `pragma abicoder v2`

> it is enabled by default starting with Solidity 0.8.0.

https://docs.soliditylang.org/en/latest/layout-of-source-files.html#abi-coder-pragma


just remove it.

4. code readability improvement.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L58

use `1e4` instead of `10000`.
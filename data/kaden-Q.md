#### WETH address lacks `address(0)` check

##### Severity: Low

##### Context:

- https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113

##### Description:

In `BlurExchange.initialize`, the `weth` address variable is set. This variable is effectively immutable since there are no methods to update it. Since there are no checks to ensure that the address isn't being set to `address(0)`, user error could lead to it being incorrectly set, requiring a costly amount of gas to be spent to redeploy the contract or potentially causing unexpected effects if gone unnoticed.

##### Remediation:

It is recommended that a `require` is added to the `initialize` method to ensure that `_weth != address(0)`.
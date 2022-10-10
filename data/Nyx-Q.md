[L-01] MISSING CHECKS FOR ADDRESS(0X0)
```
File: contracts/BlurExchange.sol

97:       address _weth,
98:       IExecutionDelegate _executionDelegate,
99:       IPolicyManager _policyManager,
100:      address _oracle,
101:      uint _blockRange

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L97

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L242-L248
```


[L-02] UPGRADE OPEN ZEPPELIN CONTRACT DEPENDENCY

An outdated OZ version is used (which has known vulnerabilities, see https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

```
"@openzeppelin/contracts": "4.4.1"
```
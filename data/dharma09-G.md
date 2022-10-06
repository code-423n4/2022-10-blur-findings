## 1.DEFAULT VALUE INITIALIZATION

### PROBLEM
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

```++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)```

### PROOF OF CONCEPT
`Instances include:`

[BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
[EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
[PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

### MITIGATION
```
for (uint256 i; i < numPages; ++i)
```
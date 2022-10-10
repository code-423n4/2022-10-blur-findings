## [L-01 ] Unchecked return value for transfer calls
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s `safeTransferFrom` unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.
### Reference
Similar medium severity issue from Consensys Diligence Audit of Fei Protocol: https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call

## Poc
There are several `transfer` calls that do not check the return value (some tokens signal failure by returning false):
```
../2022-10-blur/contracts/BlurExchange.sol:508:            payable(to).transfer(amount);
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L509-L510

## Recommendation
It is usually good to add a require-statement that checks the return value or to use something like safeTransferFrom; unless one is sure the given token reverts in case of a failure.


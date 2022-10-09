As a better practice, use `chainid()` in the constructor of `BlurExchange`: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L109
e.g:

```
uint256 chainId;
assembly {
    chainId := chainid()
}
```
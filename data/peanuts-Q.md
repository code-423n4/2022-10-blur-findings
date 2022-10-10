## Table of Contents

1. [L-01] Use SafeERC20 instead of ERC20
2. [L-02] Take out unused/unsafe function
3. [L-03] Missing Checks for address(0x0) when assigning values to address state variables


### [L-01] Use SafeERC20 instead of ERC20

SafeERC20

Wrappers around ERC20 operations that throw on failure (when the token contract returns false). Tokens that return no value (and instead revert or throw on failure) are also supported, non-reverting calls are assumed to be successful. To use this library you can add a using SafeERC20 for ERC20; statement to your contract, which allows you to call the safe operations as token.safeTransfer(…​), etc. 

https://docs.openzeppelin.com/contracts/2.x/api/token/erc20

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125

### [L-02] Take out unused/unsafe function

```
function transferERC721Unsafe(address collection, address from, address to, uint256 tokenId)
        approvedContract
        external
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L73-L79

### [L-03] Missing Checks for address(0x0) when assigning values to address state variables

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L116
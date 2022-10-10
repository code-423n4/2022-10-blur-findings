# Blur Exchange Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (3 instances)

Total 3 instances of 1 issue.

## 1. Unsafe ERC20 Operation(s) (3 instances)

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

```solidity
contracts/ExecutionDelegate.sol::78 => IERC721(collection).transferFrom(from, to, tokenId);

contracts/ExecutionDelegate.sol::125 => return IERC20(token).transferFrom(from, to, amount);

contracts/BlurExchange.sol::508 => payable(to).transfer(amount);
```

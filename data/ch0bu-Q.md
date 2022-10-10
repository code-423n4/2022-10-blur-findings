# NON-CRITICAL


## 1. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom


It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

```
ExecutionDelegate.sol
78		IERC721(collection).transferFrom(from, to, tokenId);
125		return IERC20(token).transferFrom(from, to, amount);
```

## 2. Consider adding checks for signature malleability


Use OpenZeppelin’s `ECDSA` contract rather than calling `ecrecover()` directly

```
BlurExchange.sol
408		return ecrecover(digest, v, r, s);
```

## [L-01] RETURN VALUES OF `TRANSFER()`/`TRANSFERFROM()` NOT CHECKED

Not all `IERC20` implementations `revert()` when there’s a failure in `transfer()`/`transferFrom()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

```solidity
IERC721(collection).transferFrom(from, to, tokenId);
```

[https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L78](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L78)

```solidity
return IERC20(token).transferFrom(from, to, amount);
```

[https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125)


## **[L-02] BEST PRACTICE IS TO PREVENT SIGNATURE MALLEABILITY**

Use OpenZeppelin’s `ECDSA`contract rather than calling `ecrecover()`directly

```solidity
function _recover(
        bytes32 digest,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) internal pure returns (address) {
        require(v == 27 || v == 28, "Invalid v parameter");
        return ecrecover(digest, v, r, s);
    }
```

[https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L401-L409](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L401-L409)
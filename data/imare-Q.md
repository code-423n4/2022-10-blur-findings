## [N-01] CONSIDER ADDINGS CHECKS FOR SIGNATURE MALLEABILITY

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly

There is 1 instance of this issue:

```
        return ecrecover(digest, v, r, s);
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L408

## [N-02] Add reject reason if user forgets to pay with ETH

In the following line:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452

Should be a reject reason to tell user that it needs to send ETH for the transaction

## [N-03] Remove unexisting code from tests

The ``exchange.setFeeMechanism(feeMechanism.address)`` implemention is missing from the current contract version.

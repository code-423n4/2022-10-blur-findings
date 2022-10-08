## Low risk

### [L1] In `execute()` seller != buyer is never checked
This could lead to third parties griefing users by matching their buying and sell orders. This, with the policies available now, is exploitable only in the case someone is trying to both sell and buy an ERC1155 at the same price, while paying fees; which is quite the edge case. However, in the future this could become a serious issue with introduction of more complex policies.

### [L2] `ChainId` is passed as a parameter

The `chainId` parameter at [BlurExchange.sol#L106](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L106) is passed in the constructor instead of being taken from `block.chainid`. In addition to this `DOMAIN_SEPARATOR` is a static value, but it should be dynamic to account for a potential change of `block.chainId`, in case of hardforks. 

If not fixed it could lead to replay attacks in case of hardforks or replay attacks in case of an error in setting the `chainId` during deployment, if re-deployed on another chain with the same address.

### [L3] Orders can be executed with fees up to 100%

Both buyers and sellers can generate orders with up to a 100% fee. Since the use cases for having such a high fee are not clear consider limiting the maximum possible fee, this could prevent buyers from trying to grief seller with unexpectedly high fees.

## QA

### [QA1] Use `.call` instead of `.transfer`

At [BlurExchange.sol#L508](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L508) `.transfer()` is used to transfer the ETH fees.

`transfer()` is limited to 2300 gas, which is barely enough to emit an event. If:

- The receiver is a contract that has a fallback function that consumes more than 2300 gas
- The receiver sits behind a proxy
- Gas costs are increased in the future

then some orders might not be executable.

Consider using `call()` instead, since `execute()` is protected from re-rentrancy.
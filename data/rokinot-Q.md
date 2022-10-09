## Low

### Use of deprecated library

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L3](In this line), the contract is using the abicoder v2 library, which is deprecated and turned on by default on Solidity 0.8+. [Source](https://twitter.com/solidity_lang/status/1339280545675681798)

## Non-critical

### Line spacing is off compared to other functions

[BlurExchange.sol#L298](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L298)

### Requirement is missing return string

[BlurExchange.sol#L452](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452)
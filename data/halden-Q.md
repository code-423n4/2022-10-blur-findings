#### [QA-01] Use modifer instead of duplicated requires
instead of `require(revokedApproval[from] == false, "User has revoked approval");`
use:  ` modifier HasNotRevokedApproval() `
File: ExecutionDelegate.sol line [77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77) ,  [92](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92) , [108](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108) , [124](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124)

instead of File: BlurExchange.sol line [219](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219), [228](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228),  [237](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237)
use:  ` modifier NotZeroAddress(address addr) `

#### [QA-02] Add indexed before address argument in some events
instead of ` event NonceIncremented(address trader, uint256 newNonce);`
use: ` event NonceIncremented(address indexed trader, uint256 newNonce);`
File: BlurExchange.sol line [86](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L86)

instead of ` event NewOracle(address oracle);`
use: ` event NewOracle(address indexed oracle);`
File: BlurExchange.sol line [90](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L90)
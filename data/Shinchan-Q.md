# **Low and Non-Critical**

Serial No. | Issue Name | Instances
--- | --- | ---
Q-1 | Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom | 3
Q-2 | Use of Block.Timestamp | 1
Q-3 | Use a modifier where require statement is used multiple times | 9

-----
***Total instances found in this contest: 13 | Among all files in scope***


## 1. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Instances:

[BlurExchange.sol:L508](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508)
```
contracts/BlurExchange.sol:508:            payable(to).transfer(amount);
```
[ExecutionDelegate.sol:L78](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L78)
```
contracts/ExecutionDelegate.sol:78:        IERC721(collection).transferFrom(from, to, tokenId);
```
[ExecutionDelegate.sol:L125](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125)
```
contracts/ExecutionDelegate.sol:125:        return IERC20(token).transferFrom(from, to, amount);
```
### Reference:

This [similar medium-severity finding](https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) from Consensys Diligence Audit of Fei Protocol.

### Recommended Mitigation Steps:

Consider using safeTransfer/safeTransferFrom or require() consistently.

-----
## 2. Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances:
[BlurExchange.sol:L283](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L283)
```
contracts/BlurExchange.sol:283:        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
```
### References:

https://github.com/code-423n4/2022-04-dualityfocus-findings/issues/33

-----

## 3. Use a modifier where require statement is used multiple times.

There are 3 require statements that are used multiple times in the contracts. Use a modifer instead of adding the same require statements again and again.

### Instances:
[ExecutionDelegate.sol:L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
[ExecutionDelegate.sol:L92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
[ExecutionDelegate.sol:L108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
[ExecutionDelegate.sol:L124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)
```
contracts/ExecutionDelegate.sol:77:        require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol:92:        require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol:108:        require(revokedApproval[from] == false, "User has revoked approval");
contracts/ExecutionDelegate.sol:124:        require(revokedApproval[from] == false, "User has revoked approval");
```
[BlurExchange.sol:L424](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424)
[BlurExchange.sol:L428](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428)
```
contracts/BlurExchange.sol:424:            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
contracts/BlurExchange.sol:428:            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
```
[BlurExchange.sol:L219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219)
[BlurExchange.sol:L228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228)
[BlurExchange.sol:L237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)
```
contracts/BlurExchange.sol:219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol:228:        require(address(_policyManager) != address(0), "Address cannot be zero");
contracts/BlurExchange.sol:237:        require(_oracle != address(0), "Address cannot be zero");
```
-----
Table Of Content
================

* [QA REPORT](#qa-report)
        * [Missing zero address check in a state variable setter function](#missing-zero-address-check-in-a-state-variable-setter-function)
        * [Missing zero address check for initializers functions](#missing-zero-address-check-for-initializers-functions)
        * [Unused success return value](#unused-success-return-value)
        * [Missing two steps verification process](#missing-two-steps-verification-process)
        * [Wrong use of assert](#wrong-use-of-assert)
        * [Missing 0 address check at transfer](#missing-0-address-check-at-transfer)
        * [Make sure the following functions has to be payable](#make-sure-the-following-functions-has-to-be-payable)
        * [Array access is out of bounds](#array-access-is-out-of-bounds)
        * [Use safeTransfer() instead transfer()](#use-safetransfer-instead-transfer)
        * [Add event to the following functions](#add-event-to-the-following-functions)
        * [Consider adding constant variables instead of hardcoded strings](#consider-adding-constant-variables-instead-of-hardcoded-strings)
        * [Events not emitted for important state changes](#events-not-emitted-for-important-state-changes)
        * [Missing an event after critical initialize() functions](#missing-an-event-after-critical-initialize-functions)

# QA REPORT

## Missing zero address check in a state variable setter function
A state variable of type 'address' is set without a non-zero verification. This can lead to undesired behavior.

### Code Instances:
- [L2GraphToken.sol#L59](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/token/L2GraphToken.sol#L59)
- [L1GraphTokenGateway.sol#L141](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L141)
- [Governed.sol#L31](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Governed.sol#L31)
- [L1GraphTokenGateway.sol#L131](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L131)

## Missing zero address check for initializers functions
Missing checks for zero-addresses may lead to infunctional protocol. In this case the function is an initializer then the value can be passed only once and is important to be validated. If the variable addresses are updated incorrectly.

For instance, [GraphCurationToken.sol#L26](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/curation/GraphCurationToken.sol#L26)

## Unused success return value
The following calls ignores the return value of the called function that might indicate the the call failed.

### Code Instances:
- [DisputeManager.sol#L578](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/disputes/DisputeManager.sol#L578)
- [DisputeManager.sol#L620](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/disputes/DisputeManager.sol#L620)

## Missing two steps verification process
The process of transferring ownership is dangerous since typing the wrong address can lead to severe implications. It is better to have to steps verification process with set and claim functions to decrease the chances of human error. Consider changing to two steps verification process of transferring privileges. Human mistakes can happen.

### Code Instances:
- [GraphProxyStorage.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxyStorage.sol)
- [GraphProxy.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol)
- [EthereumDIDRegistry.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/erc1056/EthereumDIDRegistry.sol)

## Wrong use of assert
You should use if-revert or require statements instead of assertions in production.

### Code Instances:
- [GraphProxy.sol#L46](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L46)
- [GraphProxy.sol#L47](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L47)

## Missing 0 address check at transfer
Some contracts does not support 0 transfer, then the transaction will revert with no explanation. We recommend to add a require statement that the amount is not 0.

### Code Instances:
- [GNS.sol#L485](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/GNS.sol#L485)
- [GRTWithdrawHelper.sol#L92](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/GRTWithdrawHelper.sol#L92)
- [L1GraphTokenGateway.sol#L275](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L275)
- [L2GraphTokenGateway.sol#L243](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/gateway/L2GraphTokenGateway.sol#L243)

## Make sure the following functions has to be payable
I didn't see a use of using payable in the following functions, consider changing it.

### Code Instances:
- [GraphProxy.sol#L199](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L199)
- [GraphProxy.sol#L191](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L191)
- [L1GraphTokenGateway.sol#L269](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L269)

## Array access is out of bounds
There is no check for the access to be in the array bounds.

### Code Instances:
- [BancorFormula.sol#L41](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/bancor/BancorFormula.sol#L41)
- [BancorFormula.sol#L496](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/bancor/BancorFormula.sol#L496)

## Use safeTransfer() instead transfer()
Use openzeppelin safeTransfer() method instead of transfer() in the following locations.

### Code Instances:
- [AllocationExchange.sol#L89](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/AllocationExchange.sol#L89)
- [L1GraphTokenGateway.sol#L275](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L275)
- [TokenUtils.sol#L19](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/utils/TokenUtils.sol#L19)
- [L1GraphTokenGateway.sol#L234](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L234)

## Add event to the following functions


### Code Instances:
- [GraphTokenUpgradeable.sol#L150](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/token/GraphTokenUpgradeable.sol#L150)
- [Governed.sol#L31](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Governed.sol#L31)
- [AllocationExchange.sol#L64](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/AllocationExchange.sol#L64)

## Consider adding constant variables instead of hardcoded strings
A good practice is to use constant variables instead of hardcoded strings in the code.

### Code Instances:
- [Staking.sol#L329](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L329)
- [Managed.sol#L56](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Managed.sol#L56)
- [RewardsManager.sol#L129](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/rewards/RewardsManager.sol#L129)
- [L1GraphTokenGateway.sol#L201](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L201)

## Events not emitted for important state changes
When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

### Code Instances:
- [GraphTokenUpgradeable.sol#L150](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/token/GraphTokenUpgradeable.sol#L150)
- [GRTWithdrawHelper.sol#L39](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/GRTWithdrawHelper.sol#L39)
- [Governed.sol#L31](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Governed.sol#L31)

## Missing an event after critical initialize() functions
To record the initialize parameters for off-chain monitoring and transparency reasons, you might find it useful to emit an event after the initialize() functions

For instance, [GraphCurationToken.sol#L26](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/curation/GraphCurationToken.sol#L26)

-
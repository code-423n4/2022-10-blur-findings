Table Of Content
================

* [QA REPORT](#qa-report)
        * [Missing zero address check for initializers functions](#missing-zero-address-check-for-initializers-functions)
        * [Contract should have pause/unpause functionality](#contract-should-have-pauseunpause-functionality)
        * [Use timelock modifier for setter functions](#use-timelock-modifier-for-setter-functions)
        * [Unused success return value](#unused-success-return-value)
        * [Use safeTransfer() instead transfer()](#use-safetransfer-instead-transfer)
        * [Missing two steps verification process](#missing-two-steps-verification-process)
        * [Make sure the following functions has to be payable](#make-sure-the-following-functions-has-to-be-payable)
        * [Missing 0 address check at transfer](#missing-0-address-check-at-transfer)
        * [Add event to the following functions](#add-event-to-the-following-functions)
        * [Events not emitted for important state changes](#events-not-emitted-for-important-state-changes)
        * [Missing an event after critical initialize() functions](#missing-an-event-after-critical-initialize-functions)
        * [Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.](#several-functions-are-declaring-named-returns-but-then-are-using-return-statements-i-suggest-choosing-only-one-for-readability-reasons)
        * [Some of the following function specification is missing](#some-of-the-following-function-specification-is-missing)
        * [Consider adding constant variables instead of hardcoded strings](#consider-adding-constant-variables-instead-of-hardcoded-strings)

# QA REPORT

## Missing zero address check for initializers functions
Missing checks for zero-addresses may lead to infunctional protocol. In this case the function is an initializer then the value can be passed only once and is important to be validated. If the variable addresses are updated incorrectly.

For instance, [BlurExchange.sol#L102](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L102)

## Contract should have pause/unpause functionality
In case a hack is occuring or an exploit is discovered, the team (or validators in this case) should be able to pause
functionality until the necessary changes are made to the system. Additionally, the gravity.sol contract should be manged by proxy so that upgrades can be made by the validators.

Because an attack would probably span a number of blocks, a method for pausing the contract would be able to interrupt any such attack if discovered.)

### Code Instances:
- [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol)
- [ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol)

## Use timelock modifier for setter functions
It is good to have a timelock for functions that set key/critical variables.

For instance, [BlurExchange.sol#L245](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L245)

## Unused success return value
The following calls ignores the return value of the called function that might indicate the the call failed.

### Code Instances:
- [BlurExchange.sol#L268](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L268)
- [BlurExchange.sol#L510](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L510)

## Use safeTransfer() instead transfer()
Use openzeppelin safeTransfer() method instead of transfer() in the following locations.

### Code Instances:
- [BlurExchange.sol#L510](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L510)
- [ExecutionDelegate.sol#L77](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L77)
- [BlurExchange.sol#L507](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L507)
- [ExecutionDelegate.sol#L124](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L124)

## Missing two steps verification process
The process of transferring ownership is dangerous since typing the wrong address can lead to severe implications. It is better to have to steps verification process with set and claim functions to decrease the chances of human error. Consider changing to two steps verification process of transferring privileges. Human mistakes can happen.

For instance, [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol)

## Make sure the following functions has to be payable
I didn't see a use of using payable in the following functions, consider changing it.

For instance, [BlurExchange.sol#L133](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L133)

## Missing 0 address check at transfer
Some contracts does not support 0 transfer, then the transaction will revert with no explanation. We recommend to add a require statement that the amount is not 0.

### Code Instances:
- [ExecutionDelegate.sol#L108](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L108)
- [BlurExchange.sol#L510](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L510)
- [BlurExchange.sol#L458](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L458)
- [ExecutionDelegate.sol#L124](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L124)

## Add event to the following functions


For instance, [BlurExchange.sol#L102](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L102)

## Events not emitted for important state changes
When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

For instance, [BlurExchange.sol#L102](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L102)

## Missing an event after critical initialize() functions
To record the initialize parameters for off-chain monitoring and transparency reasons, you might find it useful to emit an event after the initialize() functions

For instance, [BlurExchange.sol#L102](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L102)

## Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.
Using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity.

### Code Instances:
- [TestBlurExchange.sol#L20](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/test/TestBlurExchange.sol#L20)
- [BlurExchange.sol#L420](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L420)

## Some of the following function specification is missing


### Code Instances:
- [ExecutionDelegate.sol#L123](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L123)
- [PolicyManager.sol#L68](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L68)
- [BlurExchange.sol#L207](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L207)
- [TestBlurExchange.sol#L28](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/test/TestBlurExchange.sol#L28)

## Consider adding constant variables instead of hardcoded strings
A good practice is to use constant variables instead of hardcoded strings in the code.

### Code Instances:
- [BlurExchange.sol#L141](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L141)
- [ExecutionDelegate.sol#L76](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L76)
- [BlurExchange.sol#L481](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L481)
- [PolicyManager.sol#L25](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L25)

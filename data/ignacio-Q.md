# 1  CALL() SHOULD BE USED INSTEAD OF TRANSFER() ON AN ADDRESS PAYABLE
# Lines of code
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L508 
## Impact
The use of the deprecated transfer() function for an address will inevitably make the transaction fail when:

The claimer smart contract does not implement a payable function.
The claimer smart contract does implement a payable fallback which uses more than 2300 gas unit.
The claimer smart contract implements a payable fallback function that needs less than 2300 gas units but is called through proxy, raising the call’s gas usage above 2300.
Additionally, using higher than 2300 gas might be mandatory for some multisig wallets.
Whenever the user either fails to implement the payable fallback function or cumulative gas cost of the function sequence invoked on a native token transfer exceeds 2300 gas consumption limit the native tokens sent end up undelivered and the corresponding user funds return functionality will fail each time.
The impact would mean that any contracts receiving funds would potentially be unable to retrieve funds from the swap.

## Recommended Mitigation Steps
use call() to send eth , re-entrancy has been accounted for in all functions that reference Solidity's transfer() . This has been done by using a re-entrancy guard, therefore, we can rely on msg.sender.call.value(amount)` or using the OpenZeppelin Address.sendValue library


Relevant links:
https://github.com/code-423n4/2021-04-meebits-findings/issues/2
https://twitter.com/hacxyk/status/1520715516490379264?s=21&t=fnhDkcC3KpE_kJE8eLiE2A
https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/


# 2  USE OF BLOCK.TIMESTAMP
block.timestamp is being used several times
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L283


Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

## Recommended Mitigation Steps

USE A MORE RECENT VERSION OF SOLIDITY and OpenZeppelin Library
current solidity version is 0.8.15 meanwhile project is using 0.8.6.
OpenZeppelin library currently 4.7.2 while the project is using 4.4.2.
It's best to use latest version because there are some features improvement and security patch.

Use block.number instead of  block.timestamp or now to reduce the risk of
MEV attacks

### here some reference :
https://www.bookstack.cn/read/ethereumbook-en/spilt.14.c2a6b48ca6e1e33c.md
https://ethereum.stackexchange.com/questions/108033/what-do-i-need-to-be-careful-about-when-using-block-timestamp

# 3 ABI.ENCODEPACKED() SHOULD NOT BE USED WITH DYNAMIC TYPES WHEN PASSING THE RESULT TO A HASH FUNCTION SUCH AS KECCAK256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L144

# 4   RETURN VALUES OF TRANSFER()/TRANSFERFROM() NOT CHECKED
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L78
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125
# 5  USE SAFETRANSFER/SAFETRANSFERFROM CONSISTENTLY INSTEAD OF TRANSFER/TRANSFERFROM
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L93 
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L109

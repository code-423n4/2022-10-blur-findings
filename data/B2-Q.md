## `public` functions not called by the contract should be declared `external` instead
    
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from `external`to `public`

    File: contracts/lib/MerkleVerifier.sol   #1
    17    function _verifyProof(
    18    bytes32 leaf,
    19    bytes32 root,
    20    bytes32[] memory proof
    21     ) public pure {


* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17-L21

## Event is missing `indexed` fields
   
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

    File: contracts/BlurExchange.sol   #1
    76        event OrdersMatched(
    77        address indexed maker,
    78        address indexed taker,
    79        Order sell,
    80        bytes32 sellHash,
    81        Order buy,
    82        bytes32 buyHash
    83         );

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L76-L83

      File: contracts/BlurExchange.sol   #2
      86    event NonceIncremented(address trader, uint256 newNonce);

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L86

      File: contracts/BlurExchange.sol   #3
      91    event NewBlockRange(uint256 blockRange);

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L91

## Unsafe use of `transfer()`/`transferFrom()` with `IERC20`
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)‘s `transfer()` and `transferFrom()` functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to `IERC20`, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s `SafeERC20`’s `safeTransfer()`/`safeTransferFrom()` instead

    File: ExecutionDelegate.sol   #1
    125        return IERC20(token).transferFrom(from, to, amount);

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125

## Return values of `transfer()`/`transferFrom()` not checked

Not all `IERC20` implementations `revert()` when there’s a failure in `transfer()`/`transferFrom()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

    File: ExecutionDelegate.sol   #1
    125        return IERC20(token).transferFrom(from, to, amount);

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125

## Use of Block.timestamp
    
Block timestamps have historically been used for a variety of  applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

    File: contracts/BlurExchange.sol   #1
    283        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
    
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L283

## EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO `KECCAK256()`, SHOULD USE `IMMUTABLE` RATHER THAN `CONSTANT`

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

    File: contracts/lib/EIP712.sol   #1
    20    bytes32 constant public FEE_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20

      File: contracts/lib/EIP712.sol   #2
      23    bytes32 constant public ORDER_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23

      File: contracts/lib/EIP712.sol   #3
      26    bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26

      File: contracts/lib/EIP712.sol   #4
      29    bytes32 constant public ROOT_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29

     File: contracts/lib/EIP712.sol   #5
     33    bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L33

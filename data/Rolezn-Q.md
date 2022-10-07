## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Checks for Address(0x0)  | 18 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Use Safetransfer Instead Of Transfer  | 3 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Vulnerable To Cross-chain Replay Attacks Due To Static DOMAIN_SEPARATOR | 1 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Contracts are not using their OZ Upgradeable counterparts | 9 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Critical Changes Should Use Two-step Procedure | 4 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug | 2 |
| [LOW&#x2011;7](#LOW&#x2011;7) | Missing parameter validation | 1 |
| [LOW&#x2011;8](#LOW&#x2011;8) | Usage of `payable.transfer` can lead to loss of funds  | 1 |
| [LOW&#x2011;9](#LOW&#x2011;9) | Upgrade OpenZeppelin Contraact Dependency | 2 |
| [LOW&#x2011;10](#LOW&#x2011;10) | `ecrecover` may return empty address | 1 |

Total: 41 instances over 9 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Event Is Missing Indexed Fields | 2 |
| [NC&#x2011;2](#NC&#x2011;2) | Public Functions Not Called By The Contract Should Be Declared External Instead | 1 |
| [NC&#x2011;3](#NC&#x2011;3) | Constants Should Be Defined Rather Than Using Magic Numbers | 1 |
| [NC&#x2011;4](#NC&#x2011;4) | `require()` / `revert()` Statements Should Have Descriptive Reason Strings | 4 |
| [NC&#x2011;5](#NC&#x2011;5) | Implementation contract may not be initialized | 1 |
| [NC&#x2011;6](#NC&#x2011;6) | Large multiples of ten should use scientific notation | 1 |
| [NC&#x2011;7](#NC&#x2011;7) | Use of Block.Timestamp | 1 |
| [NC&#x2011;8](#NC&#x2011;8) | Non-usage of specific imports | 7 |
| [NC&#x2011;9](#NC&#x2011;9) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 5 |
| [NC&#x2011;10](#NC&#x2011;10) | Lines are too long | 1 |
| [NC&#x2011;11](#NC&#x2011;11) | Use `bytes.concat()` | 4 |
| [NC&#x2011;12](#NC&#x2011;12) | Use of `ecrecover` is susceptible to signature malleability | 1 |
| [NC&#x2011;13](#NC&#x2011;13) | Stop using `v != 27 && v != 28` or `v == 27 \|\| v == 28` | 1 |

Total: 30 instances over 13 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```
function initialize: address _weth
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L95

```
function initialize: address _oracle
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L95

```
function approveContract: address _contract
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L36

```
function denyContract: address _contract
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L45

```
function transferERC721Unsafe: address collection
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L73

```
function transferERC721Unsafe: address from
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L73

```
function transferERC721Unsafe: address to
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L73

```
function transferERC721: address collection
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L88

```
function transferERC721: address from
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L88

```
function transferERC721: address to
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L88

```
function transferERC1155: address collection
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L104

```
function transferERC1155: address from
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L104

```
function transferERC1155: address to
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L104

```
function transferERC20: address token
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L119

```
function transferERC20: address from
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L119

```
function transferERC20: address to
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L119

```
function addPolicy: address policy
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L25

```
function removePolicy: address policy
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L36



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Use Safetransfer Instead Of Transfer 

It is good to add a `require()` statement that checks the return value of token transfers or to use something like OpenZeppelin’s `safeTransfer`/`safeTransferFrom` unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

For example, Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert.

#### <ins>Proof Of Concept</ins>


```
payable(to).transfer(amount);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L508

```
IERC721(collection).transferFrom(from, to, tokenId);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L78

```
return IERC20(token).transferFrom(from, to, amount);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L125



#### <ins>Recommended Mitigation Steps</ins>

Consider using `safeTransfer`/`safeTransferFrom` or `require()` consistently.



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Vulnerable To Cross-chain Replay Attacks Due To Static DOMAIN_SEPARATOR

The protocol calculates the chainid it should assign during its execution and permanently stores it in an immutable variable. Should Ethereum fork in the feature, the chainid will change however the one used by the permits will not enabling a user to use any new permits on both chains thus breaking the token on the forked chain permanently.

Please consult EIP1344 for more details: https://eips.ethereum.org/EIPS/eip-1344#rationale

#### <ins>Proof Of Concept</ins>

```
        DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
            name              : name,
            version           : version,
            chainId           : chainId,
            verifyingContract : address(this)
        }));
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L106



#### <ins>Recommended Mitigation Steps</ins>

The mitigation action that should be applied is the calculation of the chainid dynamically on each permit invocation. As a gas optimization, the deployment pre-calculated hash for the permits can be stored to an immutable variable and a validation can occur on the permit function that ensure the current chainid is equal to the one of the cached hash and if not, to re-calculate it on the spot.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L8

```
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L5

```
import "@openzeppelin/contracts/token/ERC721/IERC721.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L6

```
import "@openzeppelin/contracts/token/ERC1155/IERC1155.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L7

```
import "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L8

```
import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L4

```
import {EnumerableSet} from "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L5

```
import "@openzeppelin/contracts/proxy/Proxy.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol#L5

```
import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Upgrade.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol#L6



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from @openzeppelin/contracts-upgradeable instead of @openzeppelin/contracts.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```
function setExecutionDelegate(IExecutionDelegate _executionDelegate)
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L215

```
function setPolicyManager(IPolicyManager _policyManager)
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L224

```
function setOracle(address _oracle)
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L233

```
function setBlockRange(uint256 _blockRange)
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L242



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

#### <ins>Proof Of Concept</ins>

POC can be found in the above medium reference url.

Functions that execute low level calls in contracts with solidity version under 0.8.14

```
function _exists(address what)
        internal
        view
        returns (bool)
    {
        uint size;
        assembly {
            size := extcodesize(what)
        }
        return size > 0;
    }
}
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L548

```
function _efficientHash(
        bytes32 a,
        bytes32 b
    ) private pure returns (bytes32 value) {
        assembly {
            mstore(0x00, a)
            mstore(0x20, b)
            value := keccak256(0x00, 0x40)
        }
    }
}
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L49





#### <ins>Recommended Mitigation Steps</ins>

Consider upgrading to at least solidity v0.8.15.



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Missing parameter validation

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```
address _logic
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol#L21



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



### <a href="#Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Usage of `payable.transfer` can lead to loss of funds 

The funds that are to be sent can be lost. The issues with `transfer()` are outlined here:
https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

#### <ins>Proof Of Concept</ins>


```
payable(to).transfer(amount);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L508



#### <ins>Recommended Mitigation Steps</ins>

Using low-level `call.value(amount)` with the corresponding result check or using the OpenZeppelin `Address.sendValue` is advised:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L60



### <a href="#Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

#### <ins>Proof Of Concept</ins>


```
@openzeppelin/contracts: 4.4.1
```

https://github.com/code-423n4/2022-10-blur/tree/main/package.json#L63

```
@openzeppelin/contracts-upgradeable: ^4.6.0
```

https://github.com/code-423n4/2022-10-blur/tree/main/package.json#L64



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json

### <a href="#Summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> `ecrecover` may return empty address

There is a common issue that `ecrecover` returns empty (0x0) address when the signature is invalid. `function _recover` should check that before returning the result of `ecrecover`.

#### <ins>Proof Of Concept</ins>


```
return ecrecover(digest, v, r, s);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L408

#### <ins>Recommended Mitigation Steps</ins>

See the solution here: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.4.0/contracts/cryptography/ECDSA.sol#L68

## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```
event OrdersMatched(
        address indexed maker,
        address indexed taker,
        Order sell,
        bytes32 sellHash,
        Order buy,
        bytes32 buyHash
    );
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L76

```
event NonceIncremented(address trader, uint256 newNonce);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L86






### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```
function cancelOrder(Order calldata order) public {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L181





### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Constants Should Be Defined Rather Than Using Magic Numbers

#### <ins>Proof Of Concept</ins>

```
    uint256 public constant INVERSE_BASIS_POINT = 10000;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L59





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> `require()` / `revert()` Statements Should Have Descriptive Reason Strings

#### <ins>Proof Of Concept</ins>


```
require(sell.order.side == Side.Sell);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L134

```
require(msg.sender == order.trader);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L183

```
require(msg.value == price);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L452

```
revert("Invalid payment token");
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L513






### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```
constructor(address _logic, bytes memory _data) payable {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol#L21





### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

#### <ins>Proof Of Concept</ins>


```
    uint256 public constant INVERSE_BASIS_POINT = 10000;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L59





### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```
        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L283



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

#### <ins>Proof Of Concept</ins>


```
import "./lib/ReentrancyGuarded.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L10

```
import "./lib/EIP712.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L11

```
import "./lib/MerkleVerifier.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L12

```
import "./interfaces/IBlurExchange.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L13

```
import "./interfaces/IExecutionDelegate.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L14

```
import "./interfaces/IPolicyManager.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L15

```
import "./interfaces/IMatchingPolicy.sol";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L16




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>

```
bytes32 constant public FEE_TYPEHASH = keccak256(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L20

```
bytes32 constant public ORDER_TYPEHASH = keccak256(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L23

```
bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L26

```
bytes32 constant public ROOT_TYPEHASH = keccak256(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L29

```
bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L33





### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```
//eips.ethereum.org/EIPS/eip-1967[EIP1967], so that it doesn't conflict with the storage layout of the
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol#L11





### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```
        return keccak256(abi.encodePacked(feeHashes)
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L80

```
        return keccak256(abi.encodePacked(
            "\x19\x01",
            DOMAIN_SEPARATOR,
            orderHash
        )
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#0

```
        return keccak256(abi.encodePacked(
            "\x19\x01",
            DOMAIN_SEPARATOR,
            keccak256(abi.encode(
                ROOT_TYPEHASH,
                root
            )
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#0

```
        return keccak256(abi.encodePacked(
            "\x19\x01",
            DOMAIN_SEPARATOR,
            keccak256(abi.encode(
                ORACLE_ORDER_TYPEHASH,
                orderHash,
                blockNumber
            )
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#0






### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Use of `ecrecover` is susceptible to signature malleability

The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to replay attacks.
References:  https://swcregistry.io/docs/SWC-117,  https://swcregistry.io/docs/SWC-121, and  https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57.
While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

#### <ins>Proof Of Concept</ins>


```
        return ecrecover(digest, v, r, s);
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L408



#### Recommended Mitigation Steps
Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.



### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Stop using `v != 27 && v != 28` or `v == 27 || v == 28`

See <a href="https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg">this</a> for reference

#### <ins>Proof Of Concept</ins>


```
v == 27 || v == 28
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L407







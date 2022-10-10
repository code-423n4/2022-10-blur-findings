# QA report

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Outdated versions of `@openzeppelin/contracts` and `@openzeppelin/contracts-upgradeable` | 2 |
| [L&#x2011;02] | Use standard libraries | 6 |
| [L&#x2011;03] | Do not pass the `chainid` as `constructor` parameter | 1 |
| [L&#x2011;04] | Missing `address(0)` checks | 5 |
| [L&#x2011;05] | `ecrecover` return `address(0)` if signature it's invalid | 1 |

Total: 15 instances over 5 issues

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Typo | 1 |
| [N&#x2011;02] | Lint | 8 |
| [N&#x2011;03] | Missing error message in `require` statement | 3 |
| [N&#x2011;04] | Unused imports | 2 |
| [N&#x2011;05] | Using `pragma abicoder v2` | 2 |
| [N&#x2011;06] | Send ether with `call` instead of `transfer` | 1 |
| [N&#x2011;07] | Mark as abstract | 1 |
| [N&#x2011;08] | Overdeclare variables | 1 |

Total: 19 instances over 8 issues

## Low Risk

### [L-01] Outdated versions of `@openzeppelin/contracts` and `@openzeppelin/contracts-upgradeable`

The latest version of `@openzeppelin/contracts` is [v4.7.3](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/v4.7.3) and of `@openzeppelin/contracts-upgradeable` is [v4.7.3](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/v4.7.3)

```solidity
File: /package.json

63    "@openzeppelin/contracts": "4.4.1",

64    "@openzeppelin/contracts-upgradeable": "^4.6.0",
```

### [L-02] Use standard libraries

Use the libraries of `@openzeppelin/contracts` to avoid mistake, this libraries there are heavy tested and are used in severals oncahin contracts

- Use [ReentrancyGuardUpgradeable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/v4.7.3/contracts/security/ReentrancyGuardUpgradeable.sol) instead of **/contracts/lib/ReentrancyGuarded.sol**

```solidity
File: /contracts/BlurExchange.sol

 10 import "./lib/ReentrancyGuarded.sol";

 30 contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {

131        reentrancyGuard
```

- Use [MerkleProofUpgradeable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/v4.7.3/contracts/utils/cryptography/MerkleProofUpgradeable.sol) instead of **/contracts/lib/MerkleVerifier.sol**

```solidity
File: /contracts/BlurExchange.sol

 12 import "./lib/MerkleVerifier.sol";

344    function _validateUserAuthorization(
345        bytes32 orderHash,
346        address trader,
347        uint8 v,
348        bytes32 r,
349        bytes32 s,
350        SignatureVersion signatureVersion,
351        bytes calldata extraSignature
352    ) internal view returns (bool) {
353        bytes32 hashToSign;
354        if (signatureVersion == SignatureVersion.Single) {
355            /* Single-listing authentication: Order signed by trader */
356            hashToSign = _hashToSign(orderHash);
357        } else if (signatureVersion == SignatureVersion.Bulk) {
358            /* Bulk-listing authentication: Merkle root of orders signed by trader */
359            (bytes32[] memory merklePath) = abi.decode(extraSignature, (bytes32[]));
360
361            bytes32 computedRoot = MerkleVerifier._computeRoot(orderHash, merklePath);
362            hashToSign = _hashToSignRoot(computedRoot);
363        }
364
365        return _recover(hashToSign, v, r, s) == trader;
366    }
```

- Use [AddressUpgradeable.sol, `isContract(address)`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/v4.7.3/contracts/utils/AddressUpgradeable.sol)

```solidity
File: /contracts/BlurExchange.sol

534        require(_exists(collection), "Collection does not exist");

548    function _exists(address what)
549        internal
550        view
551        returns (bool)
552    {
553        uint size;
554        assembly {
555            size := extcodesize(what)
556        }
557        return size > 0;
558    }
```

- Use [PausableUpgradeable.sol`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/v4.7.3/contracts/security/PausableUpgradeable.sol)

```solidity
File: /contracts/BlurExchange.sol

 33    uint256 public isOpen;

 35    modifier whenOpen() {
 36        require(isOpen == 1, "Closed");
 37        _;
 38    }

 40    event Opened();
 41    event Closed();

 43    function open() external onlyOwner {
 45        isOpen = 1;
 46        emit Opened();
 47    }
 48    function close() external onlyOwner {
 49        isOpen = 0;
 50        emit Closed();
 51    }

104        isOpen = 1;

132        whenOpen
```

- The **/contracts/lib/ERC1967Proxy.sol** it's a copy of [ERC1967Proxy.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.7/contracts/proxy/ERC1967/ERC1967Proxy.sol)

Instead of copy create an contract who heredit from **ERC1967Proxy.sol** openzeppelin contract:
```solidity
pragma solidity 0.8.13;

import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

contract ERC1967Proxy_ is ERC1967Proxy {
    constructor(address _logic, bytes memory _data) payable ERC1967Proxy(_logic, _data) {
    }
}
```

- Use [ECDSAUpgradeable.sol, `tryRecover(bytes32,uint8,bytes32,bytes32)`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/v4.7.3/contracts/utils/cryptography/ECDSAUpgradeable.sol)

```solidity
File: /contracts/BlurExchange.sol

365        return _recover(hashToSign, v, r, s) == trader;

392        return _recover(oracleHash, v, r, s) == oracle;

401    function _recover(
402        bytes32 digest,
403        uint8 v,
404        bytes32 r,
405        bytes32 s
406    ) internal pure returns (address) {
407        require(v == 27 || v == 28, "Invalid v parameter");
408        return ecrecover(digest, v, r, s);
409    }
```

### [L-03] Do not pass the `chainid` as `constructor` parameter

Use `block.chainid` instead of `chainid`

```solidity
File: /contracts/BlurExchange.sol

96        uint chainId,
```

### [L-04] Missing `address(0)` checks

```solidity
File: /contracts/BlurExchange.sol

 97        address _weth,

 98        IExecutionDelegate _executionDelegate,

 99        IPolicyManager _policyManager,

100        address _oracle,
```

```solidity
File: /contracts/PolicyManager.sol

25    function addPolicy(address policy) external override onlyOwner {
```

### [L-05] `ecrecover` return `address(0)` if signature it's invalid

In the BlurExchange.sol [`initialize`](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95-L118) the `_oracle` could be the `address(0)`
If the `_oracle` is the `address(0)`, an user can provide an invalid `order.extraSignature` and pass the [`_validateOracleAuthorization`](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#320) check because the `ecrecover` returns `address(0)` when the signature is invalid

```solidity
File: /contracts/BlurExchange.sol

365        return _recover(hashToSign, v, r, s) == trader;

392        return _recover(oracleHash, v, r, s) == oracle;

408        return ecrecover(digest, v, r, s);
```

## Non-critical

### [N-01] Typo

```solidity
File: /contracts/token/VariableSupplyERC20Token.sol

From:
19     * function call, and allows initializating the storage of the proxy like a Solidity constructor.
To:
19     * function call, and allows initializing the storage of the proxy like a Solidity constructor.
```

### [N-02] Lint

Remove space:

```solidity
File: /contracts/lib/EIP712.sol

11 \n

82 \n
```

```solidity
File: /contracts/ExecutionDelegate.sol

17 \n
```

```solidity
File: /contracts/BlurExchange.sol

 31 \n

296 \n
```

Wrong identation:

```solidity
File: /contracts/lib/EIP712.sol

 92                      ORDER_TYPEHASH,
 93                      order.trader,
 94                      order.side,
 95                      order.matchingPolicy,
 96                      order.collection,
 97                      order.tokenId,
 98                      order.amount,
 99                      order.paymentToken,
100                      order.price,
101                      order.listingTime,
102                      order.expirationTime,
103                      _packFees(order.fees),
104                      order.salt,
105                      keccak256(order.extraParams)
```

```solidity
File: /contracts/BlurExchange.sol

298          return true;
```

Don't use extra parenthesis:

```solidity
File: /contracts/BlurExchange.sol

486        return (receiveAmount);
```

### [N-03] Missing error message in `require` statements

```solidity
File: /contracts/BlurExchange.sol

134        require(sell.order.side == Side.Sell);

183        require(msg.sender == order.trader);

452            require(msg.value == price);
```

### [N-04] Unused imports

```solidity
File: /contracts/BlurExchange.sol

5 import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

8 import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
```

### [N-05] Using `pragma abicoder v2`

In the later Solidity versions it is no longer necessary to use the "experimental" version.
Using experimental constructions is not recommended for production code.

See: [abiencoderv2](https://docs.soliditylang.org/en/v0.8.7/layout-of-source-files.html#abiencoderv2)

```solidity
File: /contracts/BlurExchange.sol

3 pragma abicoder v2;
```

```solidity
File: /contracts/ExecutionDelegate.sol

3 pragma abicoder v2;
```

### [N-06] Send ether with `call` instead of `transfer`

Use call instead of transfer to send ether. And return value must be checked if sending ether is successful or not.
Sending ether with the transfer is no longer recommended.

This transaction will fail inevitably when:
1) The `_to` smart contract does not implement a payable function.
2) `_to` smart contract does implement a payable fallback which uses more than 2300 gas unit.
3) The `_to` smart contract implements a payable fallback function that needs less than 2300 gas units but is called through proxy, raising the call’s gas usage above 2300.

```solidity
File: /contracts/BlurExchange.sol

From:
508            payable(to).transfer(amount);
To:
508            (bool result, ) = to.call{value: amount}("");
509            require(result, "Failed to send Ether");
```

### [N-07] Mark as abstract

The **/contracts/lib/EIP712.sol.sol** could be mark as a abstract contract

### [N-08] Overdeclare variables

```solidity
File: /contracts/BlurExchange.sol

From:
388            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
389            v = _v; r = _r; s = _s;
To:
388            (, v, r, s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
389 <REMOVE THIS LINE>
```

- [Low](#low)
    - [**1. Outdated packages**](#1-outdated-packages)
    - [**2. Outdated compiler**](#2-outdated-compiler)
    - [**3. Allows malleable SECP256K1 signatures**](#3-allows-malleable-secp256k1-signatures)
    - [**4. Lack of ACK during owner change**](#4-lack-of-ack-during-owner-change)
    - [**5. Lack of checks**](#5-lack-of-checks)
- [Non critical](#non-critical)
    - [**6. Use abstract for base contracts**](#6-use-abstract-for-base-contracts)
    - [**7. Bad nomenclature**](#7-bad-nomenclature)
    - [**8. Do the input validations during the validation phase**](#8-do-the-input-validations-during-the-validation-phase)

# Low

## **1. Outdated packages**

Some used packages are out of date, it is good practice to use the latest version of these packages:

```
"@openzeppelin/contracts": "4.4.1",
"@openzeppelin/contracts-upgradeable": "^4.6.0",
```

**Reference:**

- https://github.com/OpenZeppelin/openzeppelin-contracts/releases

## **2. Outdated compiler**

The pragma version used are:

```
pragma solidity 0.8.13;
```

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

 ## **3. Allows malleable `SECP256K1` signatures**

Here, the `ecrecover()` method doesn't check the `s` range.

Homestead ([EIP-2](https://eips.ethereum.org/EIPS/eip-2)) added this limitation, however the precompile remained unaltered. The majority of libraries, including OpenZeppelin, do this check.

Since an order can only be confirmed once and its hash is saved, there doesn't seem to be a serious danger in existing use cases.

**Reference:**

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7201e6707f6631d9499a569f492870ebdd4133cf/contracts/utils/cryptography/ECDSA.sol#L138-L149

**Affected source code:**

- [BlurExchange.sol:408](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L408)

## **4. Lack of ACK during owner change**

It's possible to lose the ownership under specific circumstances.

Because of human error it's possible to set a new invalid owner. When you want to change the owner's address it's better to propose a new owner, and then accept this ownership with the new wallet.

**Affected source code:**

- [BlurExchange.sol:30](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L30)
- [PolicyManager.sol:13](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L13)
- [ExecutionDelegate.sol:16](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L16)

## **5. Lack of checks**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Ensure that it's a contract:**

- [BlurExchange.sol:97-100](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L97-L100)
- [BlurExchange.sol:220](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L220)
- [BlurExchange.sol:229](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L229)
- [BlurExchange.sol:238](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L238)

---

# Non critical

## **6. Use `abstract` for base contracts**

`abstract` contracts are contracts that have at least one function without its implementation. **An instance of an abstract cannot be created.**

**Reference:**

- https://docs.soliditylang.org/en/v0.6.2/contracts.html#abstract-contracts

**Affected source code:**

- [ReentrancyGuarded.sol:8](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L8)

## **7. Bad nomenclature**

The `_exists` method, according to its comment, returns if an address exists, this is not real, it returns if an address is not an EOA, which is not the same, the comment and the name of said method should be modified.

```diff
/**
-       * @dev Determine if the given address exists
+       * @dev Determine if the given address is a contract
        * @param what address to check
        */
-   function _exists(address what)
+   function _isContract(address what)
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
```

**Affected source code:**

- [BlurExchange.sol:544-558](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L544-L558)

## **8. Do the input validations during the validation phase**

The `paymentToken` is checked during the `_transferTo`, it's better to transfer all the erc20, and do this check during `_validateOrderParameters` function.

```diff
    function _transferTo(
        address paymentToken,
        address from,
        address to,
        uint256 amount
    ) internal {
        if (amount == 0) {
            return;
        }

        if (paymentToken == address(0)) {
            /* Transfer funds in ETH. */
            payable(to).transfer(amount);
-       } else if (paymentToken == weth) {
-           /* Transfer funds in WETH. */
+       } else {
            executionDelegate.transferERC20(weth, from, to, amount);
-       } else {
-           revert("Invalid payment token");
        }
    }
```

**Affected source code:**

- [BlurExchange.sol:509-514](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L509-L514)


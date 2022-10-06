- [Gas](#gas)
    - [**1. Use require instead of assert**](#1-use-require-instead-of-assert)
    - [**2. Don't use the length of an array for loops condition**](#2-dont-use-the-length-of-an-array-for-loops-condition)
    - [**3. Reduce boolean comparison**](#3-reduce-boolean-comparison)
        - [Total gas saved: **18 * 0 + 15 * 5 = 75**](#total-gas-saved-18--0--15--5--75)
    - [**4. Avoid compound assignment operator in state variables**](#4-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 2 = 26**](#total-gas-saved-13--2--26)
    - [**5. Remove empty blocks**](#5-remove-empty-blocks)
    - [**6. ++i costs less gas compared to i++ or i += 1**](#6-i-costs-less-gas-compared-to-i-or-i--1)
    - [**7. Reduce the size of error messages Long revert Strings**](#7-reduce-the-size-of-error-messages-long-revert-strings)
        - [Total gas saved: **18 * 3 = 54**](#total-gas-saved-18--3--54)
    - [**8. Use Custom Errors instead of Revert Strings to save Gas**](#8-use-custom-errors-instead-of-revert-strings-to-save-gas)
        - [Total gas saved: **9 * 26 = 234**](#total-gas-saved-9--26--234)
    - [**9. There's no need to set default values for variables**](#9-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 7 = 56**](#total-gas-saved-8--7--56)
    - [**10. Change bool to uint256 can save gas**](#10-change-bool-to-uint256-can-save-gas)
    - [**11. Remove unnecessary variables**](#11-remove-unnecessary-variables)
    - [**12. constants expressions are expressions, not constants**](#12-constants-expressions-are-expressions-not-constants)
    - [**13. Optimize EIP712._hashOrder**](#13-optimize-eip712_hashorder)
    - [**14. Optimize PolicyManager.addPolicy**](#14-optimize-policymanageraddpolicy)
    - [**15. Optimize PolicyManager.removePolicy**](#15-optimize-policymanagerremovepolicy)
    - [**16. Change bool to uint256 can save gas**](#16-change-bool-to-uint256-can-save-gas)
    - [**17. delete optimization**](#17-delete-optimization)
        - [Total gas saved: **5 * 2 = 10**](#total-gas-saved-5--2--10)
    - [**18. Optimize BlurExchange._validateOracleAuthorization**](#18-optimize-blurexchange_validateoracleauthorization)

# Gas

## **1. Use `require` instead of `assert`**

The `assert()` and `require()` functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the `assert()` function when false, **uses up all the remaining gas and reverts all the changes made.**

Meanwhile, a `require()` function when false, also reverts back all the changes made to the contract but **does refund all the remaining gas fees we offered to pay**. This is the most common Solidity function used by developers for debugging and error handling.

**Affected source code:**

- [ERC1967Proxy.sol:22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L22)

## **2. Don't use the length of an array for loops condition**

It's cheaper to store the length of the array inside a local variable and iterate over it.

**Affected source code:**

- [BlurExchange.sol:199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol:476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
- [EIP712.sol:77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
- [MerkleVerifier.sol:38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

## **3. Reduce boolean comparison**

Compare a boolean value using `== true` or `== false` instead of using the boolean value is more expensive.

`NOT` opcode it's cheaper than using `EQUAL` or `NOTEQUAL` when the value it's false, or just the value without `== true` when it's true, because it will use less opcodes inside the VM.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == true; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return a; }
}
```

Gas saving executing: **18 per entry for == true**

```
TesterA.testEqual:   21814
TesterB.testNot:     21796   
```

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == false; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return !a; }
}
```

Gas saving executing: **15 per entry for == false**

```
TesterA.testEqual:   21814
TesterB.testNot:     21799
```

**Affected source code:**

Use `!` instead of `== false`:

- [BlurExchange.sol:267](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L267)
- [ExecutionDelegate.sol:77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
- [ExecutionDelegate.sol:92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
- [ExecutionDelegate.sol:108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
- [ExecutionDelegate.sol:124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)

### Total gas saved: **(18 * 0) + (15 * 5) = 75**

## **4. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
uint private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [BlurExchange.sol:208](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208)
- [BlurExchange.sol:479](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L479)

### Total gas saved: **13 * 2 = 26**

## **5. Remove empty blocks**

An empty block or an empty method generally indicates a lack of logic that it’s unnecessary and should be eliminated, call a method that literally does nothing only consumes gas unnecessarily, if it is a `virtual` method which is expects it to be filled by the class that implements it, it is better to use `abstract`  contracts with just the definition.

**Sample code:**

```javascript
pragma solidity >=0.4.0 <0.7.0;

abstract contract BaseContract {
    function totalSupply() public virtual returns (uint256);
}
```

**Reference:**
- https://docs.soliditylang.org/en/v0.6.0/contracts.html?highlight=abstract#abstract-contracts

**Affected source code:**

- [BlurExchange.sol:53](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L53)

## **6. `++i` costs less gas compared to `i++` or `i += 1`**

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

```solidity
uint i = 1;
i++; // == 1 but i == 2
```

But `++i` returns the actual incremented value:

```solidity
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`
I suggest using `++i` instead of `i++` to increment the value of an uint variable. Same thing for `--i` and `i--`

*Keep in mind that this change can only be made when we are not interested in the value returned by the operation, since the result is different, you only have to apply it when you only want to increase a counter.*

**Affected source code:**

- [BlurExchange.sol:199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol:476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
- [PolicyManager.sol:77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
- [EIP712.sol:77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)
- [MerkleVerifier.sol:38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)

## **7. Reduce the size of error messages (Long revert Strings)**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testShortRevert(bool path) public view {
require(path, "test error");
}
}

contract TesterB {
function testLongRevert(bool path) public view {
require(path, "test big error message, more than 32 bytes");
}
}
```

Gas saving executing: **18 per entry**

```
TesterA.testShortRevert: 21886
TesterB.testLongRevert:  21904
```

**Affected source code:**

- [BlurExchange.sol:318](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
- [BlurExchange.sol:482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)
- [ExecutionDelegate.sol:22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)

### Total gas saved: **18 * 3 = 54**

## **8. Use Custom Errors instead of Revert Strings to save Gas**

Custom errors from Solidity `0.8.4` are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

**Source Custom Errors in Solidity:**

Starting from Solidity `v0.8.4`, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testRevert(bool path) public view {
 require(path, "test error");
}
}

contract TesterB {
error MyError(string msg);
function testError(bool path) public view {
 if(path) revert MyError("test error");
}
}
```

Gas saving executing: **9 per entry**

```
TesterA.testRevert: 21611
TesterB.testError:  21602     
```

**Affected source code:**

- [BlurExchange.sol:36](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L36)
- [BlurExchange.sol:134](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134)
- [BlurExchange.sol:139](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139)
- [BlurExchange.sol:140](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L140)
- [BlurExchange.sol:142](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L142)
- [BlurExchange.sol:143](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L143)
- [BlurExchange.sol:183](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L183)
- [BlurExchange.sol:219](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219)
- [BlurExchange.sol:228](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228)
- [BlurExchange.sol:237](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)
- [BlurExchange.sol:318](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
- [BlurExchange.sol:407](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407)
- [BlurExchange.sol:424](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424)
- [BlurExchange.sol:428](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428)
- [BlurExchange.sol:431](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L431)
- [BlurExchange.sol:452](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452)
- [BlurExchange.sol:482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)
- [BlurExchange.sol:534](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534)
- [ExecutionDelegate.sol:22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
- [ExecutionDelegate.sol:77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
- [ExecutionDelegate.sol:92](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
- [ExecutionDelegate.sol:108](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
- [ExecutionDelegate.sol:124](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)
- [PolicyManager.sol:26](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26)
- [PolicyManager.sol:37](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)
- [ReentrancyGuarded.sol:14](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L14)

### Total gas saved: **9 * 26 = 234**

## **9. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384
```

**Affected source code:**

- [BlurExchange.sol:475](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475)
- [ReentrancyGuarded.sol:10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)
- [MerkleVerifier.sol:38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
- [BlurExchange.sol:199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
- [BlurExchange.sol:476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
- [PolicyManager.sol:77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
- [EIP712.sol:77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77)

### Total gas saved: **8 * 7 = 56**

## **10. Change `bool` to `uint256` can save gas**

Because each write operation requires an additional `SLOAD` to read the slot's contents, replace the bits occupied by the boolean, and then write back, `booleans` are more expensive than `uint256` or any other type that uses a complete word. This cannot be turned off because it is the compiler's defense against pointer aliasing and contract upgrades.

Reference:

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27

Also, this is applicable to integer types different from `uint256` or `int256`.

**Affected source code for `booleans`:**

- [ReentrancyGuarded.sol:10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)
- [ExecutionDelegate.sol:18](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L18)
- [ExecutionDelegate.sol:19](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L19)

## **11. Remove unnecessary variables**

The following state variables can be removed without affecting the logic of the contract since they are not used and/or are redundant because they could be used inline.

**Affected source code:**

- Remove `computedRoot` in [MerkleVerifier.sol:22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L22)
- Remove `proofElement` in [MerkleVerifier.sol:39](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L39)

## **12. `constants` expressions are expressions, not `constants`**

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

**Reference:**

- https://github.com/ethereum/solidity/issues/9232

> Consequences: each usage of a "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..). since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library).

**Affected source code:**

- [EIP712.sol:20-35](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L35)

## **13. Optimize `EIP712._hashOrder`**

Remove the `bytes.concat` and change the method like this:

```diff
    function _hashOrder(Order calldata order, uint256 nonce)
        internal
        pure
        returns (bytes32)
    {
        return keccak256(
-           bytes.concat(
                abi.encode(
                      ORDER_TYPEHASH,
                      order.trader,
                      order.side,
                      order.matchingPolicy,
                      order.collection,
                      order.tokenId,
                      order.amount,
                      order.paymentToken,
                      order.price,
                      order.listingTime,
                      order.expirationTime,
                      _packFees(order.fees),
                      order.salt,
+                     keccak256(order.extraParams),
+                     nonce
-                     keccak256(order.extraParams)
-               ),
-               abi.encode(nonce)
            )
        );
    }
```

**Affected source code:**

- [EIP712.sol:84-110](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L84-L110)

## **14. Optimize `PolicyManager.addPolicy`**

The method `add` already checks if the element is already present in the `AddressSet`.

It's recommended to apply the following changes:

```diff
    function addPolicy(address policy) external override onlyOwner {
-       require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
-       _whitelistedPolicies.add(policy);
+       require(_whitelistedPolicies.add(policy), "Already whitelisted");

        emit PolicyWhitelisted(policy);
    }
```

**Affected source code:**

- [PolicyManager.sol:25-30](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L25-L30)

## **15. Optimize `PolicyManager.removePolicy`**

The method `remove` already checks if the element is already present in the `AddressSet`.

It's recommended to apply the following changes:

```diff
    function removePolicy(address policy) external override onlyOwner {
+       require(_whitelistedPolicies.remove(policy), "Not whitelisted");
-       require(_whitelistedPolicies.contains(policy), "Not whitelisted");
-       _whitelistedPolicies.remove(policy);

        emit PolicyRemoved(policy);
    }
```

**Affected source code:**

- [PolicyManager.sol:36-41](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L36-L41)

## **16. Change `bool` to `uint256` can save gas**

Because each write operation requires an additional `SLOAD` to read the slot's contents, replace the bits occupied by the boolean, and then write back, `booleans` are more expensive than `uint256` or any other type that uses a complete word. This cannot be turned off because it is the compiler's defense against pointer aliasing and contract upgrades.

Reference:

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27

Also, this is applicable to integer types different from `uint256` or `int56`.

**Affected source code for `booleans`:**

- [ExecutionDelegate.sol:18](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L18)
- [ExecutionDelegate.sol:19](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L19)
- [BlurExchange.sol:71](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L71)

## **17. `delete` optimization**

Use `delete` instead of set to default value (`false` or `0`).

5 gas could be saved per entry in the following affected lines:

**Affected source code:**

- [ExecutionDelegate.sol:46](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L46)
- [ExecutionDelegate.sol:62](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L62)
    
### Total gas saved: **5 * 2 = 10**

## **18. Optimize `BlurExchange._validateOracleAuthorization`**

If the Bulk signatures are stored like the single, but with the `merklePath` at the end, instead of at the begining, it could be easier to parse because it's not required to check the `signatureVersion`.

```diff
        uint8 v; bytes32 r; bytes32 s;
-       if (signatureVersion == SignatureVersion.Single) {
            (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
-       } else if (signatureVersion == SignatureVersion.Bulk) {
-           /* If the signature was a bulk listing the merkle path musted be unpacked before the oracle signature. */
-           (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
-           v = _v; r = _r; s = _s;
        }
```

**Affected source code:**

- [BlurExchange.sol:383-390](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L383-L390)
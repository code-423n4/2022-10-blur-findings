### `Require` revert string is too long
The revert strings below can be shortened to 32 characters or fewer (as shown) to save gas (or else consider using custom error codes, which could save even more gas)

___
[ExecutionDelegate.sol: L22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
```solidity
        require(contracts[msg.sender], "Contract is not approved to make transfers");
```
Change message to `Contract not approved for xfers`
___
[BlurExchange.sol: L482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)
```solidity
        require(totalFee <= price, "Total amount of fees are more than the price");
```
Change message to `Total fees exceed the price`
___
___

### Inline a function/modifier that is only used once
It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.
___
[BlurExchange.sol: L35-38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L35-L38)
```solidity
    modifier whenOpen() {
        require(isOpen == 1, "Closed");
        _;
    }
```
Since `whenOpen()` is used only once in this contract (in `function execute()`) as shown below, it should be inlined to save gas

[BlurExchange.sol: L128-133](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L128-L133)
```solidity
    function execute(Input calldata sell, Input calldata buy)
        external
        payable
        reentrancyGuard
        whenOpen
    {
```
___
___


### State variables should not be initialized to their default values
For example, initializing `uint` variables to their default value of `0` is unnecessary and costs gas
___
[ReentrancyGuarded.sol: L10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10)
```solidity
    bool reentrancyLock = false;
```
Change to `bool reentrancyLock;`
___
[BlurExchange.sol: L475](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475)
```solidity
        uint256 totalFee = 0;
```
Change to: 
```solidity
        uint256 totalFee;
```
___
___

### Array length should not be looked up during every iteration of a `for` loop
Since calculating the array length costs gas, it's best to read the length of the array from memory before executing the loop, as demonstrated below:

[EIP712.sol: L77-79](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77-L79)
```solidity
        for (uint256 i = 0; i < fees.length; i++) {
            feeHashes[i] = _hashFee(fees[i]);
        }
```
Suggestion:
```solidity
        uint256 totalFeesLength = fees.length; 
        for (uint256 i = 0; i < totalFeesLength; i++) {
            feeHashes[i] = _hashFee(fees[i]);
        }
```
___
[BlurExchange.sol: L199-201](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199-L201)
```solidity
        for (uint8 i = 0; i < orders.length; i++) {
            cancelOrder(orders[i]);
        }
```
Suggestion:
```solidity
        uint256 totalOrdersLength = orders.length; 
        for (uint256 i = 0; i < totalOrdersLength; i++) {
            cancelOrder(orders[i]);
        }
```
___
[BlurExchange.sol: L476-480](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476-L480)
```solidity
        for (uint8 i = 0; i < fees.length; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }
```
Suggestion:
```solidity
        uint256 totalFeesLen = fees.length; 
        for (uint256 i = 0; i < totalFeesLen; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }
```
___
[MerkleVerifier.sol: L38-41](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38-L41)
```solidity
        for (uint256 i = 0; i < proof.length; i++) {
            bytes32 proofElement = proof[i];
            computedHash = _hashPair(computedHash, proofElement);
        }
```
Suggestion:
```solidity
        uint256 totalProofLength = proof.length; 
        for (uint256 i = 0; i < totalProofLength; i++) {
            bytes32 proofElement = proof[i];
            computedHash = _hashPair(computedHash, proofElement);
        }
```
___
___


### Use `++i` instead of `i++` to increase count in a `for` loop
Since use of  `i++` (or equivalent counter) costs more gas, it is better to use `++i`, as demonstrated below: 

[PolicyManager.sol: L77-79](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77-L79)
```solidity
        for (uint256 i = 0; i < length; i++) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        }
```
Suggestion:
```solidity
        for (uint256 i = 0; i < length; ++i) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        }
```
SimiIarly for the following `for` loops:

[EIP712.sol: L77-79](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77-L79)

[BlurExchange.sol: L199-201](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199-L201)

[BlurExchange.sol: L476-480](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476-L480)

[MerkleVerifier.sol: L38-41](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38-L41)
___
___


### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}` instead, as shown below:

[PolicyManager.sol: L77-79](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77-L79)
```solidity
        for (uint256 i = 0; i < length; i++) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        }
```
Suggestion:
```solidity
        for (uint256 i = 0; i < length;) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);

            unchecked {
              ++i;
          }
        }
```
SimiIarly for the following `for` loops:

[EIP712.sol: L77-79](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77-L79)

[BlurExchange.sol: L199-201](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199-L201)

[BlurExchange.sol: L476-480](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476-L480)

[MerkleVerifier.sol: L38-41](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38-L41)
___
___


### Avoid storage of uints or ints smaller than 32 bytes, if possible
Storage of uints or ints smaller than 32 bytes incurs overhead. Instead, use size of at least 32, then downcast where needed
___

[OrderStructs.sol: L8-11](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol#L8-L11)
```solidity
struct Fee {
    uint16 rate;
    address payable recipient;
}
```
___
[BlurExchange.sol: L344-352](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L344-L352)
```solidity
    function _validateUserAuthorization(
        bytes32 orderHash,
        address trader,
        uint8 v,
        bytes32 r,
        bytes32 s,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature
    ) internal view returns (bool) {
```
___
[BlurExchange.sol: L396-406](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L396-L406)
```solidity
     * @dev Wrapped ecrecover with safety check for v parameter
     * @param v v
     * @param r r
     * @param s s
     */
    function _recover(
        bytes32 digest,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) internal pure returns (address) {
```
___
[OrderStructs.sol: L30-38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol#L30-L38)
```solidity
struct Input {
    Order order;
    uint8 v;
    bytes32 r;
    bytes32 s;
    bytes extraSignature;
    SignatureVersion signatureVersion;
    uint256 blockNumber;
}
```
___
Recommendation: Increase the sizes of `rate` and `v` to at least `uint32`
___
___

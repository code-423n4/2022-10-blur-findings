1 - Add if statement in order to save gas
==

The process of the function could be avoided if there are not any fees, so an "if statement" could verify the fee.

```
contracts/BlurExchange.sol#L469         function _transferFees(
```

Example at the beginning of the function:

```
if (fee.length == 0) return price
```

**Links:**

[contracts/BlurExchange.sol#L469](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L469)


2 - Calculate the hash value and hardcode in the code could save gas
==

The hash calculations of each variable and put them in the code could save gas in the deployment

Instances of this issue:

```
contracts/lib/EIP712.sol#L20          bytes32 constant public FEE_TYPEHASH = keccak256(
contracts/lib/EIP712.sol#L23          bytes32 constant public ORDER_TYPEHASH = keccak256(
contracts/lib/EIP712.sol#L26          bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
contracts/lib/EIP712.sol#L29          bytes32 constant public ROOT_TYPEHASH = keccak256(
contracts/lib/EIP712.sol#L33          bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```

**LInks:**

[contracts/lib/EIP712.sol#L20-L35](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L35)

3 - Use immutable variables can save gas
==

Variables that are assigned only in the constructor can be immutable

Instances of this issue:

```
contracts/BlurExchange.sol#L63           address public weth;
```

**LInks:**

[contracts/BlurExchange.sol#L63](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L63)

4 - Internal functions only called once can be inlined to save gas
==

Not inlining costs more gas because of extra ```JUMP``` instructions and additional stack operations needed for function calls.

Instances of this issue:

```
contracts/BlurExchange.sol#L416          function _canMatchOrders(Order calldata sell, Order calldata buy)
contracts/BlurExchange.sol#L548          function _exists(address what)
```

**Links:**

[contracts/BlurExchange.sol#L416](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L416)
[contracts/BlurExchange.sol#L548](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L548)

5 - Unnecessary checked arithmetic in for loop
==

There is no risk that the loop counter can overflow, using solidity's unchecked block saves gas.

Instance of this issue:

```
contracts/PolicyManager.sol#L77          for (uint256 i = 0; i < length; i++) {
contracts/BlurExchange.sol#L199          for (uint8 i = 0; i < orders.length; i++) {
contracts/lib/MerkleVerifier.sol#L38     for (uint256 i = 0; i < proof.length; i++) {
contracts/BlurExchange.sol#L476          for (uint8 i = 0; i < fees.length; i++) {
```

Unchecked implementation example:

```
for (uint256 i; i < 10;) {
    j++;
    unchecked {
        ++i;
    }
}
```

**Links:**

[contracts/PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
[contracts/BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
[contracts/lib/MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
[contracts/BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

6 - Cache Array Length Outside of Loop
==

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Instances of this issue:
```
contracts/BlurExchange.sol#L199          for (uint8 i = 0; i < orders.length; i++) {
contracts/lib/MerkleVerifier.sol#L38     for (uint256 i = 0; i < proof.length; i++) {
contracts/BlurExchange.sol#L476          for (uint8 i = 0; i < fees.length; i++) {

```

**Links:**

[contracts/BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
[contracts/lib/MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
[contracts/BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)

7 - Using private rather than public for constants save gas
==

If needed, the values can be read from the verified contract source code or from a specific function.

Instances of this:
```
contracts/BlurExchange.sol#L57     string public constant name = "Blur Exchange";
contracts/BlurExchange.sol#L58     string public constant version = "1.0";
contracts/BlurExchange.sol#L59     uint256 public constant INVERSE_BASIS_POINT = 10000;
```

**Links:**

[contracts/BlurExchange.sol#L57-L59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L59)

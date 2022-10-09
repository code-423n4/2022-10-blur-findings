## Summary

### Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [G-01] | Variables unnecessarily initialized with default values | 2 |
| [G-02] | Dynamic Keccak256 calls for static text | 1 |
| [G-03] | Use custom errors instead of revert()/require() strings | 2 |
| [G-04] | Combine address mappings into a single map to a struct | 1 |
| [G-05] | Prefix increment/decrement (++i/--i) is cheaper than postfix increment/decrement | 5 |
| [G-06] | Unnecessary overflow checks in loop | 3 |
| [G-07] | Cache array length outside of loops | 4 |
| [G-08] | Using += and -= is less gas efficient than X = X + Y | 2 |
| [G-09] | Unoptimized subtract after underflow check | 1 |
| [G-10] | Unoptimized public constants | 3 |
| [G-11] | Use a modifier instead of duplicating require statements | 1 |
| [G-12] | Where practical, use Bytes32 instead of string. | 4 |
| [G-13] | Use != 0 instead of > 0 for unsigned integer comparison | 1 |

30 Instances of 13 distinct gas optimization issues were identified.

## Gas Optimizations

### [G-01]  Variables unnecessarily initialized with default values
In Solidity each type has a default value. Setting that value is unnecessary and costs gas.

*2 Instances of this issue were found:*
```solidity
  contracts/lib/BlurExchange.sol:
         475┆ uint256 totalFee = 0;
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/BlurExchange.sol#L475

```solidity
  contracts/lib/ReentrancyGuarded.sol:
         10┆ bool reentrancyLock = false;
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10

### [G-02] Dynamic Keccak256 calls for static text
Calling keccak256() unnecessarily wastes gas both for the KECCAK256 opcode to the gas required to the associated required stack functions ot call the opcode. When using a keccak256 encoded string literal saving the result to a constant saves gas. As an example, the following code could be optimized:

```solidity
  contracts/lib/ERC1967Proxy.sol:
         22┆ assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L22

Consider replacing the dynamic call with a precalculated constant value. The precalculated value for the \_IMPLEMENTATION_SLOT comparison is:

```solidity
bytes32 constant EIP1967_PROXY_IMPLEMENTATION = 0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc;
```

*1 Instance of this issue was found.*

### [G-03] Use custom errors instead of revert()/require() strings
Solidity version 0.8.4 introduced custom errors, which save around 50 gas per call by not having to allocate and store the error string. This is particularly significant when an error string is longer than 32 bytes (256 bits) in size.

*2 Instances of this issue were found:*

```solidity
    contracts/BlurExchange.sol:
         482┆ require(totalFee <= price, "Total amount of fees are more than the price");
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

```solidity
    contracts/ExecutionDelegate.sol:
         22┆ require(contracts[msg.sender], "Contract is not approved to make transfers");
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22

### [G-04] Combine address mappings into a single map to a struct
Instead of using multiple address mappings, consider using a single address mapping that maps to a struct. This saves a storage slot and up to 20k in gas dependeing on whether a variable is assigned a zero (2900 gas), non-zero (20k gas) value or is unassigned but public (non-payable getter deployment cost is saved).

*1 Instance of this issue was found:*

```solidity
    contracts/ExecutionDelegate.sol:
         18┆ mapping(address => bool) public contracts;
         19┆ mapping(address => bool) public revokedApproval;
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L18-19

### [G-05] Prefix increment/decrement (++i/--i) is cheaper than postfix increment/decrement

(i++) Postfix increment (i++) stores the original value in memory, incremements it, stores the temporary value then retrieves it using 4 instructions. Using prefix increment (++i) increments the value and returns it using 2 instructions. This issue compounds in loops. A similar situation exists for i-.

*5 Instances of this issue were found:*

```solidity
    contracts/BlurExchange.sol:
        199┆ for (uint8 i = 0; i < orders.length; i++) {
        476┆ for (uint8 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

```solidity
    contracts/lib/EIP712.sol:
         77┆ for (uint256 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

```solidity
    contracts/lib/MerkleVerifier.sol:
         38┆ for (uint256 i = 0; i < proof.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

```solidity
    contracts/PolicyManager.sol:
         77┆ for (uint256 i = 0; i < length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

### [G-06] Unnecessary overflow checks in loop

From Solidity 0.8.0 onwards, if the loop counter cannot overflow, using `unchecked { ++i }` can save 30 gas per iteration over `i++`. This is because the unchecked approach removes overflow checks that would be run on each loop iteration. Ensure that the counter cannot overflow before implementing any changes.

*3 Instances of this issue were found:*

```solidity
    contracts/lib/EIP712.sol:
         77┆ for (uint256 i = 0; i < fees.length; i++) {
         78┆     feeHashes[i] = _hashFee(fees[i]);
         79┆ }
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77-L79

```solidity
    contracts/lib/MerkleVerifier.sol:
         38┆ for (uint256 i = 0; i < proof.length; i++) {
         39┆     bytes32 proofElement = proof[i];
         40┆     computedHash = _hashPair(computedHash, proofElement);
         41┆ }
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38-L41

```solidity
    contracts/PolicyManager.sol:
         77┆ for (uint256 i = 0; i < length; i++) {
         78┆     whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
         79┆ }
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77-L79

### [G-07] Cache array length outside of loops

It's cheaper to calculate an array's length once prior to using it in a loop than to calculate an array length inside a loop on each iteration. The first iteration has no extra cost compared to a one-off .length calculation. Each following iteration includes the following gas cost:

* Gwarmaccess for storage arrays (100 gas)
* MLOAD for memory arrays (3 gas)
* CALLDATALOAD for calldata arrays (3 gas)

*4 Instances of this issue were found:*

```solidity
    contracts/BlurExchange.sol:
        199┆ for (uint8 i = 0; i < orders.length; i++) {
        476┆ for (uint8 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

```solidity
    contracts/lib/EIP712.sol:
         77┆ for (uint256 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

```solidity
    contracts/lib/MerkleVerifier.sol:
         38┆ for (uint256 i = 0; i < proof.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

### [G-08] Using += and -= is less gas efficient than X = X + Y
Using `X += Y` costs over 100 gas more than `X = X + Y`.

*2 Instances of this issue were found:*

```solidity
    contracts/BlurExchange.sol:
        208┆ nonces[msg.sender] += 1;
        479┆ totalFee += fee;
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479

### [G-09] Unoptimized subtract after underflow check
Where operands cannot underflow because of a prior require() or if statement, it should be safe to use unchecked {} for subtractions. Using unchecked {} can save 30 gas.

*1 Instance of this issue was found:*

```solidity
    contracts/BlurExchange.sol:
        482┆ require(totalFee <= price, "Total amount of fees are more than the price");
        483┆ 
        484┆ /* Amount that will be received by seller. */
        485┆ uint256 receiveAmount = price - totalFee;
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482-L485

### [G-10] Unoptimized public constants
The solc compiler creates a getter function for each public constant. This adds extra deployment gas costs, and an entry in the method ID table. This doesn't happen with private constants. Using a separate function to return a tuple of constants is usually cheaper where multiple constants are used.

*3 Instances of this issue were found:*

```solidity
    contracts/BlurExchange.sol:
         57┆ string public constant name = "Blur Exchange";
         58┆ string public constant version = "1.0";
         59┆ uint256 public constant INVERSE_BASIS_POINT = 10000;
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482-L485

### [G-11] Use a modifier instead of duplicating require statements
The more often an identical require statement is used, the cheaper it is in deployment costs to use a modifier. 4 Identical require statements using this code were identified:

*1 Instance of this issue was found:*

```solidity
	contracts/ExecutionDelegate.sol:
		require(revokedApproval[from] == false, "User has revoked approval");
```

This statement occurs in the [transferERC721Unsafe()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L66-L79), [transferERC721()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L81-L94), [transferERC1155()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L96-L110), and [transferERC20()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L112-L126) functions. These functions are the only functions to use the [approvedContract()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L21-L24) modifier. Consider moving the require statements into the modifier.
### [G-12] Where practical, use Bytes32 instead of string.
The EVM treats string state variables as a dynamically sized type, but bytes32 is fixed. Because of this, bytes32 has a lower gas overhead. Use bytes32 for shorter strings to save gas.

*4 Instances of this issue was found:*

```solidity
	contracts/BlurExchange.sol:
		/* Constants */
		string public constant name = "Blur Exchange";
		string public constant version = "1.0";
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L58

```solidity
	contracts/lib/EIP712.sol:
		struct EIP712Domain {
			string name; // Might be better as bytes32
			string version; // Might be better as bytes32
			uint256 chainId;
			address verifyingContract;
		}
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L13-L14

### [G-13] Use != 0 instead of > 0 for unsigned integer comparison
A small gas saving can be obtained by replacing > 0 with !=0 when comparing unsigned integers. Unsigned integers don't underflow on modern solidity versions.

*1 Instance of this issue was found.*

```solidity
	contracts/lib/EIP712.sol:
		function _exists(address what)
			internal
			view
			returns (bool)
		{
			uint size;
			assembly {
				size := extcodesize(what)
			}
			return size > 0; // use return size != 0;
		}
```
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L557


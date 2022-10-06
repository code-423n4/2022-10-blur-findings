## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `++i` Costs Less Gas Than `i++`, Especially When It’s Used In For-loops (`--i`/`i--` Too) | 5 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate | 1 |
| [GAS&#x2011;3](#GAS&#x2011;3) | Using Private Rather Than Public For Constants, Saves Gas | 3 |
| [GAS&#x2011;4](#GAS&#x2011;4) | `<array>.length` Should Not Be Looked Up In Every Loop Of A For-loop | 4 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas | 4 |
| [GAS&#x2011;6](#GAS&#x2011;6) | It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied | 3 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Empty Blocks Should Be Removed Or Emit Something | 1 |
| [GAS&#x2011;8](#GAS&#x2011;8) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 2 |
| [GAS&#x2011;9](#GAS&#x2011;9) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 5 |
| [GAS&#x2011;10](#GAS&#x2011;10) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 2 |
| [GAS&#x2011;11](#GAS&#x2011;11) | Using Bools For Storage Incurs Overhead | 3 |
| [GAS&#x2011;12](#GAS&#x2011;12) | `abi.encode()` Is Less Efficient Than `abi.encodepacked()` | 6 |
| [GAS&#x2011;13](#GAS&#x2011;13) | Don’t Compare Boolean Expressions To Boolean Literals | 4 |
| [GAS&#x2011;14](#GAS&#x2011;14) | Use calldata instead of memory for function parameters | 3 |
| [GAS&#x2011;15](#GAS&#x2011;15) | Use assembly to check for `address(0)` | 1 |
| [GAS&#x2011;16](#GAS&#x2011;16) | Public Functions To External | 4 |
| [GAS&#x2011;17](#GAS&#x2011;17) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 3 |
| [GAS&#x2011;18](#GAS&#x2011;18) | Use of Custom Errors Instead of String | 23 |
| [GAS&#x2011;19](#GAS&#x2011;19) | Optimize names to save gas | 7 |

Total: 84 instances over 19 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `++i` Costs Less Gas Than `i++`, Especially When It's Used In For-loops (`--i`/`i--` Too)

Saves 6 gas per loop

#### <ins>Proof Of Concept</ins>


```
for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L199

```
for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L476

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L77

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L38



#### <ins>Recommended Mitigation Steps</ins>

For example, use `++i` instead of `i++`



### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

#### <ins>Proof Of Concept</ins>


```
mapping(address => bool) public contracts;
mapping(address => bool) public revokedApproval;

```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L18






### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Using Private Rather Than Public For Constants, Saves Gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

#### <ins>Proof Of Concept</ins>


```
    string public constant name = "Blur Exchange";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L57

```
    string public constant version = "1.0";
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L58

```
    uint256 public constant INVERSE_BASIS_POINT = 10000;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L59



#### <ins>Recommended Mitigation Steps</ins>

Set variable to private.



### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> `<array>.length` Should Not Be Looked Up In Every Loop Of A For-loop

The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

#### <ins>Proof Of Concept</ins>

```
for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L199

```
for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L476

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L77

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L38






### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas

#### <ins>Proof Of Concept</ins>


```
function _hashToSign(bytes32 orderHash)
        internal
        view
        returns (bytes32 hash)
    {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L112

```
function _hashToSignRoot(bytes32 root)
        internal
        view
        returns (bytes32 hash)
    {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L124

```
function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
        internal
        view
        returns (bytes32 hash)
    {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L139

```
function _implementation() internal view virtual override returns (address impl) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol#L29






### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

#### <ins>Proof Of Concept</ins>


```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L77

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L38






### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```
function _authorizeUpgrade(address) internal override onlyOwner {}
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L53






### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```
nonces[msg.sender] += 1;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L208

```
totalFee += fee;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L479





### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```
for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L199

```
for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L476

```
for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L77

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L38





### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```
require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L318

```
require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L482





### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Using Bools For Storage Incurs Overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

#### <ins>Proof Of Concept</ins>


```
    mapping(bytes32 => bool) public cancelledOrFilled;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L71

```
    mapping(address => bool) public contracts;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L18

```
    mapping(address => bool) public revokedApproval;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L19





### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> `abi.encode()` Is Less Efficient Than `abi.encodepacked()`

#### <ins>Proof Of Concept</ins>


```
abi.encode(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L45

```
abi.encode(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L61

```
abi.encode(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L91

```
abi.encode(nonce)
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L107

```
keccak256(abi.encode(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L132

```
keccak256(abi.encode(
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L147





### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Don't Compare Boolean Expressions To Boolean Literals

For cases of: if (<x> == true), use if (<x>) instead
For cases of: if (<x> == false), use if (!<x>) instead

#### <ins>Proof Of Concept</ins>

```
require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L77

```
require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L92

```
require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L108

```
require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L124





### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:

contract C {
function add(uint[] memory arr) external returns (uint sum) {
uint length = arr.length;
for (uint i = 0; i < arr.length; i++) {
    sum += arr[i];
}
}
}
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:

contract C {
function add(uint[] calldata arr) external returns (uint sum) {
uint length = arr.length;
for (uint i = 0; i < arr.length; i++) {
    sum += arr[i];
}
}
}
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:

function f(bytes memory data) external {
(...) = abi.decode(data, (..., types, ...));
}
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>


```
function viewWhitelistedPolicies(uint256 cursor, uint256 size)
        external
        view
        override
        returns (address[] memory, uint256)
    {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L63

```
function _verifyProof(
        bytes32 leaf,
        bytes32 root,
        bytes32[] memory proof
    ) public pure {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L17

```
function _computeRoot(
        bytes32 leaf,
        bytes32[] memory proof
    ) public pure returns (bytes32) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L33






### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> Use assembly to check for `address(0)`

Save 6 gas per instance if using assembly to check for address(0)

e.g.
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}

#### <ins>Proof Of Concept</ins>


```
function setOracle(address _oracle)
        external
        onlyOwner
    {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L233






### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```
function initialize(
        uint chainId,
        address _weth,
        IExecutionDelegate _executionDelegate,
        IPolicyManager _policyManager,
        address _oracle,
        uint _blockRange
    ) public initializer {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L95

```
function cancelOrder(Order calldata order) public {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L181

```
function _verifyProof(
        bytes32 leaf,
        bytes32 root,
        bytes32[] memory proof
    ) public pure {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L17

```
function _computeRoot(
        bytes32 leaf,
        bytes32[] memory proof
    ) public pure returns (bytes32) {
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L33






### <a href="#Summary">[GAS&#x2011;17]</a><a name="GAS&#x2011;17"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contractâ€™s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```
uint8 v;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L383

```
uint16 rate;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/OrderStructs.sol#L9

```
uint8 v;
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/OrderStructs.sol#L32






### <a href="#Summary">[GAS&#x2011;18]</a><a name="GAS&#x2011;18"> Use of Custom Errors Instead of String

To save some gas the use of custom errors leads to cheaper deploy time cost and run time cost. The run time cost is only relevant when the revert condition is met.

#### <ins>Proof Of Concept</ins>


```
File: \2022-10-blur\contracts\BlurExchange.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L534

```
File: \2022-10-blur\contracts\ExecutionDelegate.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L77

```
File: \2022-10-blur\contracts\PolicyManager.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L37

```
File: \2022-10-blur\contracts\lib\ReentrancyGuarded.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ReentrancyGuarded.sol#L14



#### <ins>Recommended Mitigation Steps</ins>

Use Custom Errors instead of strings.



### <a href="#Summary">[GAS&#x2011;19]</a><a name="GAS&#x2011;19"> Optimize names to save gas

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://github.com/enzosv/solidity-optimize-name) link for an example of how it works. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### <ins>Proof Of Concept</ins>

```
File: \2022-10-blur\contracts\ExecutionDelegate.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol

```
File: \2022-10-blur\contracts\PolicyManager.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol

```
File: \2022-10-blur\contracts\lib\EIP712.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol

```
File: \2022-10-blur\contracts\lib\ERC1967Proxy.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol

```
File: \2022-10-blur\contracts\lib\ReentrancyGuarded.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ReentrancyGuarded.sol

```
File: \2022-10-blur\contracts\matchingPolicies\StandardPolicyERC1155.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/matchingPolicies/StandardPolicyERC1155.sol

```
File: \2022-10-blur\contracts\matchingPolicies\StandardPolicyERC721.sol
```

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/matchingPolicies/StandardPolicyERC721.sol







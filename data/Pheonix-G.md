|  | Issue | Instances |
| --- | --- | --- |
| [G-01] | ++icosts less gas than i++, especially when it's used in for-loops (--i/i-- too) | 5 |
| [G-02] | Use Custom Errors instead of Revert Strings to save Gas | 26 |
| [G-03] | Cache the length of arrays in loops | 4 |
| [G-04] | Using private rather than public for constants, saves gas | 7 |
| [G-05] | Constant expressions | 1 |
| [G-06] | Using bool for storage incurs overhead | 3 |
| [G-07] | abi.encode() is less efficient than abi.encodePacked() | 4 |
| [G-08] | It costs more gas to initialize variables with their default value than letting the default value be applied | 5 |
| [G-09] | Using storage instead of memory for structs/arrays | 2 |

## `++i`costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration)

For a `uint256 i` variable, the following is true with the Optimizer enabled

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

`i++` increments `i` and returns the initial value of `i`. Which means:

```
uint i = 1;
i++; // == 1 but i == 2

```

But `++i` returns the actual incremented value:

```
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable

```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`

# **Instances** -

### **File - BlurExchange.sol**

199:     for (uint8 i = 0; i < orders.length; i++) {
476:     for (uint8 i = 0; i < fees.length; i++) {

### **File- PolicyManager.sol**

77:  for (uint256 i = 0; i < length; i++) {
``

### **File- EIP712.sol**

77:  for (uint256 i = 0; i < fees.length; i++) {

### **File- MerkleVerifier.sol**

38:  for (uint256 i = 0; i < proof.length; i++) {

### Recommended Mitigation Steps

Replace i++ with ++i

## **Use Custom Errors instead of Revert Strings to save Gas**

1. Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries). see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

## **INSTANCES**

### **File - BlurExchange.sol**

 36: require(isOpen == 1, "Closed");

134: require(sell.order.side == Side.Sell);

139:         require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140:         require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

142:         require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143:         require(_validateSignatures(buy, buyHash), "Buy failed authorization");

183:         require(msg.sender == order.trader);

219:         require(address(_executionDelegate) != address(0), "Address cannot be zero");
228: require(address(_policyManager) != address(0), "Address cannot be zero");

237: require(_oracle != address(0), "Address cannot be zero");

318: require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

407: require(v == 27 || v == 28, "Invalid v parameter");

424: require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

428: require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

431: require(canMatch, "Orders cannot be matched");

452: require(msg.value == price);

482: require(totalFee <= price, "Total amount of fees are more than the price");

534: require(_exists(collection), "Collection does not exist");

### **File - ExecutionDelegate.sol**

22: require(contracts[msg.sender], "Contract is not approved to make transfers");

77: require(revokedApproval[from] == false, "User has revoked approval"); 

92: require(revokedApproval[from] == false, "User has revoked approval");

108: require(revokedApproval[from] == false, "User has revoked approval");

124: require(revokedApproval[from] == false, "User has revoked approval");

### **File - ReentrancyGuarded.sol**

14: require(!reentrancyLock, "Reentrancy detected");

### **File - PolicyManager.sol**

26: require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

37: require(_whitelistedPolicies.contains(policy), "Not whitelisted");

### Recommended Mitigation Steps

Replace require and revert statements with custom errors.

## **Cache the length of arrays in loops**

The solidity compiler will always read the length of the array during each iteration. That is,

1.if it is a storage array, this is an extra sload operation (**100 additional extra gas (EIP-2929 2) for each iteration except for the first**),
2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):

Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

The overheads outlined below are *PER LOOP*, excluding the first loop

- storage arrays incur a Gwarmaccess (**100 gas**)
- memory arrays use `MLOAD` (**3 gas**)
- calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

## **INSTANCES -**

### File - BlurExchange.sol

199:

`for (uint256 i = 0; i < proof.length; i++) {`

476:

`for (uint8 i = 0; i < fees.length; i++) {`

### File - MerkleVerifier.sol

38:

`for (uint256 i = 0; i < proof.length; i++) {`

### **File - EIP712.sol**

77:

`for (uint256 i = 0; i < fees.length; i++) {`

### Recommended Mitigation Steps

Caching the length in a variable before the `for` loop.

## **Using `private` rather than `public` for constants, saves gas**

If needed, the values can be read from the verified contract source 
code, or if there are multiple values there can be a single getter 
function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable 
getter functions for deployment calldata, not having to store the bytes 
of the value outside of where it's used, and not adding another entry to
the method ID table

## **Instances -**

### **File - BlurExchange.sol**

57:     string public constant name = "Blur Exchange";
58:     string public constant version = "1.0";
59:     uint256 public constant INVERSE_BASIS_POINT = 10000;

### **File - EIP712.sol**

20:     bytes32 constant public FEE_TYPEHASH = keccak256(
21:         "Fee(uint16 rate,address recipient)"
22:     );

23:     bytes32 constant public ORDER_TYPEHASH = keccak256(
24:         "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
25:     );

26:     bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
27:         "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
28:     );

29:     bytes32 constant public ROOT_TYPEHASH = keccak256(
30:         "Root(bytes32 root)"
31:     );

### Recommended Mitigation Steps

Make the constants `private` instead of `public`.

## **Constant expressions**

Constant expressions are [re-calculated each time they are in use](https://github.com/ethereum/solidity/issues/9232) , costing an extra `97`
 gas than a constant every time they are called.

## Instances -

### **File - EIP712.sol**

33:     bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
34:         "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
35:     );

### Recommended Mitigation Steps

Mark these as `immutable` instead of `constant`.

## **Using `bool` for storage incurs overhead**

Booleans are more expensive than uint256 or any type that 
takes up a full word because each write operation emits an extra SLOAD 
to first read the slot's contents, replace the bits taken up by the 
boolean, and then write back. This is the compiler's defense against 
contract upgrades and pointer aliasing, and it cannot be disabled.

Consider doing like here: [https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) . They are using `uint256(1)` and `uint256(2)` for true/false to avoid the extra SLOAD (**100 gas**) and the extra SSTORE (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past:

## **Instances -**

### **File - BlurExchange.sol**

71: mapping(bytes32 => bool) public cancelledOrFilled;

### **File - ExecutionDelegate.sol**

state mapping - 

18:     mapping(address => bool) public contracts;
19:     mapping(address => bool) public revokedApproval;

### File:ReentrancyGuarded.sol

10:     bool reentrancyLock = false;

## **abi.encode() is less efficient than abi.encodePacked()**

Changing `abi.encode` function to `abi.encodePacked`
 can save gas since the `abi.encode` function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient (see [Solidity-Encode-Gas-Comparison](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)).

### File: EIP712.sol

45:             abi.encode(
46:                 EIP712DOMAIN_TYPEHASH,
47:                 keccak256(bytes([eip712Domain.name](http://eip712domain.name/))),
48:                 keccak256(bytes(eip712Domain.version)),
49:                 eip712Domain.chainId,
50:                 eip712Domain.verifyingContract
51:             )

61:             abi.encode(
62:                 FEE_TYPEHASH,
63:                 fee.rate,
64:                 fee.recipient
65:             )
66:         );

091:                 abi.encode(
092:                       ORDER_TYPEHASH,
093:                       order.trader,
094:                       order.side,
095:                       order.matchingPolicy,
096:                       order.collection,
097:                       order.tokenId,
098:                       order.amount,
099:                       order.paymentToken,
100:                       order.price,
101:                       order.listingTime,
102:                       order.expirationTime,
103:                       _packFees(order.fees),
104:                       order.salt,
105:                       keccak256(order.extraParams)
106:                 ),

107:                 abi.encode(nonce)
108:             )
109:         );

129:         return keccak256(abi.encodePacked(
130:             "\x19\x01",
131:             DOMAIN_SEPARATOR,
132:             keccak256(abi.encode(
133:                 ROOT_TYPEHASH,
134:                 root
135:             ))
136:         ));
137:     }

143:     {

### Recommended Mitigation Steps

Change  `abi.encode` function to `abi.encodePacked`

## **It costs more gas to initialize variables with their default value than letting the default value be applied**

If a variable is not set/initialized, it is assumed to have the default value (`0`
for `uint`, `false`for `bool`, `address(0)`for address...). Explicitly initializing it with its default value is an anti-pattern and wastes gas (around **3 gas** per instance).

## **Instances -**

### File: BlurExchange.sol

199:         for (uint8 i = 0; i < orders.length; i++) {

476:         for (uint8 i = 0; i < fees.length; i++) {

### File: EIP712.sol

77:         for (uint256 i = 0; i < fees.length; i++) {

### File: MerkleVerifier.sol

38:         for (uint256 i = 0; i < proof.length; i++) {

### File: PolicyManager.sol

77:         for (uint256 i = 0; i < length; i++) {

### Recommended Mitigation Steps

Consider removing explicit initializations for default values.

## **Using `storage` instead of `memory` for structs/arrays**

When fetching data from a storage location, assigning the data to a `memory`
 variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas** ) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory`
 keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack  variables, will be much cheaper, only incuring the Gcoldsload for the 
fields actually read. The only time it makes sense to read the whole 
struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory` , or if the array/struct is being read from another `memory`array/struct

## **Instances -**

### File: EIP712.sol

74:         bytes32[] memory feeHashes = new bytes32[](

### File: PolicyManager.sol

75:         address[] memory whitelistedPolicies = new address;
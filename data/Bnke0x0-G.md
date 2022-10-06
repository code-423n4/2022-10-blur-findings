

## Issues found

### [G01] State variables only set in the constructor should be declared `immutable`


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::33 => uint256 public isOpen;
2022-10-blur/contracts/BlurExchange.sol::63 => address public weth;
2022-10-blur/contracts/BlurExchange.sol::64 => IExecutionDelegate public executionDelegate;
2022-10-blur/contracts/BlurExchange.sol::65 => IPolicyManager public policyManager;
2022-10-blur/contracts/BlurExchange.sol::66 => address public oracle;
2022-10-blur/contracts/BlurExchange.sol::67 => uint256 public blockRange;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor, avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::63 => address public weth;
2022-10-blur/contracts/BlurExchange.sol::66 => address public oracle;
```


### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```



### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```



### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
2022-10-blur/contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
```



### [G06] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::365 => return _recover(hashToSign, v, r, s) == trader;
2022-10-blur/contracts/BlurExchange.sol::392 => return _recover(oracleHash, v, r, s) == oracle;
2022-10-blur/contracts/PolicyManager.sol::48 => return _whitelistedPolicies.contains(policy);
2022-10-blur/contracts/PolicyManager.sol::55 => return _whitelistedPolicies.length();
```



### [G07] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::48 => isOpen = 0;
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```



### [G08] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```


### [G09] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead



#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::347 => uint8 v,
2022-10-blur/contracts/BlurExchange.sol::383 => uint8 v; bytes32 r; bytes32 s;
2022-10-blur/contracts/BlurExchange.sol::385 => (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
2022-10-blur/contracts/BlurExchange.sol::388 => (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
2022-10-blur/contracts/BlurExchange.sol::403 => uint8 v,
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::21 => "Fee(uint16 rate,address recipient)"
2022-10-blur/contracts/lib/OrderStructs.sol::9 => uint16 rate;
2022-10-blur/contracts/lib/OrderStructs.sol::32 => uint8 v;
```



### [G10] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

#### Impact
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue
#### Findings:
```
2022-10-blur/contracts/lib/EIP712.sol::20 => bytes32 constant public FEE_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::23 => bytes32 constant public ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::26 => bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::29 => bytes32 constant public ROOT_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::33 => bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/ERC1967Proxy.sol::22 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```



### [G11] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::424 => require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
2022-10-blur/contracts/BlurExchange.sol::428 => require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

2022-10-blur/contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::92 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::108 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::124 => require(revokedApproval[from] == false, "User has revoked approval");
```



### [G12] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::219 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::237 => require(_oracle != address(0), "Address cannot be zero");
```



### [G13] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::36 => require(isOpen == 1, "Closed");
2022-10-blur/contracts/BlurExchange.sol::134 => require(sell.order.side == Side.Sell);
2022-10-blur/contracts/BlurExchange.sol::139 => require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
2022-10-blur/contracts/BlurExchange.sol::140 => require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
2022-10-blur/contracts/BlurExchange.sol::142 => require(_validateSignatures(sell, sellHash), "Sell failed authorization");
2022-10-blur/contracts/BlurExchange.sol::143 => require(_validateSignatures(buy, buyHash), "Buy failed authorization");
2022-10-blur/contracts/BlurExchange.sol::183 => require(msg.sender == order.trader);
2022-10-blur/contracts/BlurExchange.sol::219 => require(address(_executionDelegate) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::237 => require(_oracle != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::318 => require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
2022-10-blur/contracts/BlurExchange.sol::407 => require(v == 27 || v == 28, "Invalid v parameter");
2022-10-blur/contracts/BlurExchange.sol::424 => require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
2022-10-blur/contracts/BlurExchange.sol::428 => require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
2022-10-blur/contracts/BlurExchange.sol::431 => require(canMatch, "Orders cannot be matched");
2022-10-blur/contracts/BlurExchange.sol::452 => require(msg.value == price);
2022-10-blur/contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
2022-10-blur/contracts/BlurExchange.sol::513 => revert("Invalid payment token");
2022-10-blur/contracts/BlurExchange.sol::534 => require(_exists(collection), "Collection does not exist");
2022-10-blur/contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
2022-10-blur/contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::92 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::108 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::124 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/PolicyManager.sol::26 => require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
2022-10-blur/contracts/PolicyManager.sol::37 => require(_whitelistedPolicies.contains(policy), "Not whitelisted");
2022-10-blur/contracts/lib/MerkleVerifier.sol::24 => revert InvalidProof();
2022-10-blur/contracts/lib/ReentrancyGuarded.sol::14 => require(!reentrancyLock, "Reentrancy detected");
```



### [G14] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::43 => function open() external onlyOwner {
2022-10-blur/contracts/BlurExchange.sol::47 => function close() external onlyOwner {
2022-10-blur/contracts/BlurExchange.sol::53 => function _authorizeUpgrade(address) internal override onlyOwner {}
2022-10-blur/contracts/ExecutionDelegate.sol::36 => function approveContract(address _contract) onlyOwner external {
2022-10-blur/contracts/ExecutionDelegate.sol::45 => function denyContract(address _contract) onlyOwner external {
2022-10-blur/contracts/PolicyManager.sol::25 => function addPolicy(address policy) external override onlyOwner {
2022-10-blur/contracts/PolicyManager.sol::36 => function removePolicy(address policy) external override onlyOwner {
```



### [G15] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.13 to have external calls skip
 contract existence checks if the external call has a return value
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/ExecutionDelegate.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/PolicyManager.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/lib/EIP712.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/lib/ERC1967Proxy.sol::3 => pragma solidity 0.8.13;
2022-10-blur/contracts/lib/MerkleVerifier.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/lib/OrderStructs.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/lib/ReentrancyGuarded.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/matchingPolicies/StandardPolicyERC1155.sol::2 => pragma solidity 0.8.13;
2022-10-blur/contracts/matchingPolicies/StandardPolicyERC721.sol::2 => pragma solidity 0.8.13;
```


### [G16] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

There are several cases of function arguments using memory instead of calldata
#### Findings:
```
2022-10-blur/contracts/lib/EIP712.sol::39 => function _hashDomain(EIP712Domain memory eip712Domain)
```
### Recommended Mitigation Steps

Change function arguments from memory to calldata.







### [G17] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::508 => payable(to).transfer(amount);
2022-10-blur/contracts/ExecutionDelegate.sol::78 => IERC721(collection).transferFrom(from, to, tokenId);
2022-10-blur/contracts/ExecutionDelegate.sol::125 => return IERC20(token).transferFrom(from, to, amount);
```



### [G18] Unnecessary `initialize()` function

#### Impact
The initialize() function isn’t an initializer.
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::95 => function initialize(
```

### [G19] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
2022-10-blur/contracts/ExecutionDelegate.sol::18 => mapping(address => bool) public contracts;
2022-10-blur/contracts/ExecutionDelegate.sol::19 => mapping(address => bool) public revokedApproval;
```


### [G20] Using `bools` for storage incurs overhead


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::71 => mapping(bytes32 => bool) public cancelledOrFilled;
2022-10-blur/contracts/BlurExchange.sol::421 => bool canMatch;
2022-10-blur/contracts/ExecutionDelegate.sol::18 => mapping(address => bool) public contracts;
2022-10-blur/contracts/ExecutionDelegate.sol::19 => mapping(address => bool) public revokedApproval;
2022-10-blur/contracts/lib/ReentrancyGuarded.sol::10 => bool reentrancyLock = false;
```



### [G21] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable 
getter functions for deployment calldata, and not adding another entry 
to the method ID table
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::57 => string public constant name = "Blur Exchange";
2022-10-blur/contracts/BlurExchange.sol::58 => string public constant version = "1.0";
2022-10-blur/contracts/BlurExchange.sol::59 => uint256 public constant INVERSE_BASIS_POINT = 10000;
2022-10-blur/contracts/lib/EIP712.sol::20 => bytes32 constant public FEE_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::23 => bytes32 constant public ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::26 => bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::29 => bytes32 constant public ROOT_TYPEHASH = keccak256(
```



### [G22] Usage of `assert()` instead of `require()`

#### Impact
Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions).require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require, unless a Panic really is intended.
#### Findings:
```
2022-10-blur/contracts/lib/ERC1967Proxy.sol::22 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```



### [G23] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::53 => function _authorizeUpgrade(address) internal override onlyOwner {}
```



### [G24] Remove or replace unused state variables

#### Impact
Saves a storage slot. If the variable is assigned a non-zero value, 
saves Gsset (20000 gas). If it’s assigned a zero value, saves Gsreset 
(2900 gas). If the variable remains unassigned, there is no gas savings 
unless the variable is public, in which case the 
compiler-generated non-payable getter deployment cost is saved. If the 
state variable is overriding an interface’s public function, mark the 
variable as constant or immutable so that it does not use a storage slot
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::388 => (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```



### [G25] `keccak256()` should only need to be called on a specific string literal once

#### Impact
It should be saved to an immutable variable, and the variable used 
instead. If the hash is being used as a part of a function selector, the
 cast to bytes4 should also only be done once
#### Findings:
```
2022-10-blur/contracts/lib/EIP712.sol::29 => bytes32 constant public ROOT_TYPEHASH = keccak256(
```



### [G26] `abi.encode()` is less efficient than abi.encodePacked()



#### Findings:
```
2022-10-blur/contracts/lib/EIP712.sol::45 => abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::61 => abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::91 => abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::107 => abi.encode(nonce)
2022-10-blur/contracts/lib/EIP712.sol::132 => keccak256(abi.encode(
2022-10-blur/contracts/lib/EIP712.sol::147 => keccak256(abi.encode(
```



### [G27] Don’t compare boolean expressions to boolean literals

#### Impact
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::267 => (cancelledOrFilled[orderHash] == false) &&
2022-10-blur/contracts/ExecutionDelegate.sol::77 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::92 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::108 => require(revokedApproval[from] == false, "User has revoked approval");
2022-10-blur/contracts/ExecutionDelegate.sol::124 => require(revokedApproval[from] == false, "User has revoked approval");
```





[c4udit](https://github.com/Bnke0x0/c4udit)
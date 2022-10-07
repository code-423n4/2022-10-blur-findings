##  Multiple `address` mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

    File: contracts/ExecutionDelegate.sol   #1
    18          mapping(address => bool) public contracts;
    19          mapping(address => bool) public revokedApproval;

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L18-L19

## `internal` functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

    File: contracts/lib/EIP712.sol   #1
    55    function _hashFee(Fee calldata fee)
    56         internal 

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L55-L56

      File: contracts/lib/EIP712.sol   #2
      69    function _packFees(Fee[] calldata fees)
      70      internal

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L69-L70

      File: contracts/BlurExchange.sol   #3
      344    function _validateUserAuthorization(
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L344-L352

      File: contracts/BlurExchange.sol   #4
      375    function _validateOracleAuthorization(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L375-L380

      File: contracts/BlurExchange.sol   #5
      401    function _recover(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L401-L409

##  `<array>.length` should not be looked up in every loop of a `for`-loop

The solidity compiler will always read the length of the array during each iteration. That is,
    
1. If it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
2. If it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
3. If it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)
    
This extra costs can be avoided by caching the array length (in stack):
    
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

    File: contracts/lib/MerkleVerifier.sol   #1
    38        for (uint256 i = 0; i < proof.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

      File: contracts/lib/EIP712.sol   #2
      77        for (uint256 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

      File: contracts/BlurExchange.sol   #3
      199       for (uint8 i = 0; i < orders.length; i++) {
      
*  https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

       File: contracts/BlurExchange.sol   #4
       476        for (uint8 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

## `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
    
The `unchecked`keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 
This saves 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

    File: contracts/lib/MerkleVerifier.sol   #1
    38        for (uint256 i = 0; i < proof.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

      File: contracts/lib/EIP712.sol   #2
      77        for (uint256 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

      File: contracts/PolicyManager.sol   #3
      77        for (uint256 i = 0; i < length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

      File: contracts/BlurExchange.sol   #4
      199       for (uint8 i = 0; i < orders.length; i++) {
      
*  https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

       File: contracts/BlurExchange.sol   #5
       476        for (uint8 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

## `abi.encode()` is less efficient than `abi.encodePacked()`

    File: contracts/lib/EIP712.sol   #1
    45            abi.encode(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L45

      File: contracts/lib/EIP712.sol   #2
      61            abi.encode(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L61

      File: contracts/lib/EIP712.sol   #3
      91                abi.encode(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L91

      File: contracts/lib/EIP712.sol   #4
      132             keccak256(abi.encode(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L132

      File: contracts/lib/EIP712.sol   #5
      147             keccak256(abi.encode(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L147

## Using `bool`s for storage incurs overhead
    
Use uint256(1)and uint256(2)for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from false to true, after having been true in the past

    File: contracts/ExecutionDelegate.sol   #1  
    18    mapping(address => bool) public contracts;

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L18

      File: contracts/ExecutionDelegate.sol   #2
      19    mapping(address => bool) public revokedApproval;

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L19

      File: contracts/BlurExchange.sol   #3
      71     mapping(bytes32 => bool) public cancelledOrFilled;

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L71

## It costs more gas to initialize variables to zero than to let the default of zero be applied

When a variable is declared solidity assigns the default value. In case the contract assigns the value again, it costs extra gas.
Example:uint x = 0 costs more gas than uint x without having any different functionality.

    File: contracts/lib/MerkleVerifier.sol   #1
    38        for (uint256 i = 0; i < proof.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

      File: contracts/lib/EIP712.sol   #2
      77        for (uint256 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

      File: contracts/PolicyManager.sol   #3
      77        for (uint256 i = 0; i < length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

      File: contracts/BlurExchange.sol   #4
      199       for (uint8 i = 0; i < orders.length; i++) {
      
*  https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

       File: contracts/BlurExchange.sol   #5
       475        uint256 totalFee = 0;

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475

       File: contracts/BlurExchange.sol   #6
       476        for (uint8 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

## `internal` functions not called by the contract should be removed to save deployment gas
    
If the functions are required by an interface, the contract should inherit from that interface and use the `override`

    File: contracts/lib/ERC1967Proxy.sol   #1
    29    function _implementation() internal view virtual override returns (address impl) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L29

      File: contracts/lib/EIP712.sol   #2
      39    function _hashDomain(EIP712Domain memory eip712Domain)
      40        internal   

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L39-L40

      File: contracts/lib/EIP712.sol   #3
      84    function _hashOrder(Order calldata order, uint256 nonce)
      85        internal

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L84-L85

      File: contracts/lib/EIP712.sol   #4
      112    function _hashToSign(bytes32 orderHash)
      113        internal

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L112-L113

      File: contracts/lib/EIP712.sol   #5
      124    function _hashToSignRoot(bytes32 root)
      125        internal

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L124_L125

      File: contracts/lib/EIP712.sol   #6
      139    function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
      140       internal

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L139-L140

      File: contracts/BlurExchange.sol  #7
      53    function _authorizeUpgrade(address) internal override onlyOwner {}

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53

##  `++i` costs less gas than `i++`, especially when it’s used in `for`-loops (`--i`/`i--` too)

Saves 6 gas PER LOOP

    File: contracts/lib/MerkleVerifier.sol   #1
    38        for (uint256 i = 0; i < proof.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

      File: contracts/lib/EIP712.sol   #2
      77        for (uint256 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

      File: contracts/PolicyManager.sol   #3
      77        for (uint256 i = 0; i < length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

      File: contracts/BlurExchange.sol   #4
      199       for (uint8 i = 0; i < orders.length; i++) {
      
*  https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

       File: contracts/BlurExchange.sol   #5
       476        for (uint8 i = 0; i < fees.length; i++) {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476


##  Using `private` rather than `public` for constants, saves gas
    
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

    File: contracts/BlurExchange.sol   #1
    57        string public constant name = "Blur Exchange";

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57

      File: contracts/BlurExchange.sol   #2
      58    string public constant version = "1.0";

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58

      File: contracts/BlurExchange.sol   #3
      59    uint256 public constant INVERSE_BASIS_POINT = 10000;

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59

## Use of constant keccak variables results in extra hashing (and so gas).

Changing the variables from constant to immutable will reduce keccak operations and save gas.

    File: contracts/lib/EIP712.sol   #1
    20    bytes32 constant public FEE_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20

      File: contracts/lib/EIP712.sol   #2
      23    bytes32 constant public ORDER_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23

      File: contracts/lib/EIP712.sol   #3
      26    bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26

      File: contracts/lib/EIP712.sol   #4
      29    bytes32 constant public ROOT_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29

      File: contracts/lib/EIP712.sol   #5
      33    bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L33

## `Inline` a `modifier` that's only used `once`

As `whenOpen()`is only used once in this contract (in `function execute()`), it should get inlined to save gas without losing readability.

    File: contracts/BlurExchange.sol   #1
    35    modifier whenOpen() {

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L35

## Same calculations are done twice

    File: contracts/PolicyManager.sol   #1
    71        if (length > _whitelistedPolicies.length() - cursor) {
    72        length = _whitelistedPolicies.length() - cursor;

function `viewWhitelistedPolicies()`, here the same calculations are done twice:`(_whitelistedPolicies.length() - cursor)`

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L71-L72

## Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

    File: contracts/BlurExchange.sol   #1
    53    function _authorizeUpgrade(address) internal override onlyOwner {}

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53

##  Use custom errors rather than `revert()`/`require()` strings to save deployment gas
    
Custom errors are available from solidity version 0.8.4. Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. 

The instances below match or exceed that version deployment gas:

* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L140
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L142
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L143
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482
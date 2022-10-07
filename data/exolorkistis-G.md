[1] ``<ARRAY>.LENGTH`` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A`` FOR``-LOOP

The overheads outlined below are *PER LOOP*, excluding the first loop

-storage arrays incur a Gwarmaccess (100 gas)
-memory arrays use MLOAD (3 gas)
-calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.

*There are 5 instances of this issue:*

```
File: 2022-10-blur/contracts/PolicyManager.sol

77: for (uint256 i = 0; i < length; i++) {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)


```
File: 2022-10-blur/contracts/lib/EIP712.sol

77: for (uint256 i = 0; i < fees.length; i++) {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

```
File: 2022-10-blur/contracts/BlurExchange.sol

199: for (uint8 i = 0; i < orders.length; i++) {

476: for (uint8 i = 0; i < fees.length; i++) {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)


```
File: 2022-10-blur/contracts/lib/MerkleVerifier.sol

38:  for (uint256 i = 0; i < proof.length; i++) {

```

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)



[2] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
 
The ``unchecked`` keyword is new in solidity 0.8.0,so this only applies to that version or higher, which these instances are. This saves 30-40 gas *[PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)*

*There are 5 instances of this issue:*

```
File: 2022-10-blur/contracts/PolicyManager.sol

77: for (uint256 i = 0; i < length; i++) {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

```
File: 2022-10-blur/contracts/lib/EIP712.sol

77: for (uint256 i = 0; i < fees.length; i++) {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

```
File: 2022-10-blur/contracts/BlurExchange.sol

199: for (uint8 i = 0; i < orders.length; i++) {

476: for (uint8 i = 0; i < fees.length; i++) {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

```
File: 2022-10-blur/contracts/lib/MerkleVerifier.sol

38:  for (uint256 i = 0; i < proof.length; i++) {

```

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)



[3] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED ``PAYABLE``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are ``CALLVALUE``(2),``DUP1``(3),``ISZERO``(3),``PUSH``2(3),``JUMPI``(10),``PUSH1``(3),``DUP1``(3),``REVERT``(0),``JUMPDEST``(1),``POP``(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

*There are 11 instances of this issue:*


```
File: 2022-10-blur/contracts/PolicyManager.sol

25: function addPolicy(address policy) external override onlyOwner

36:  function removePolicy(address policy) external override onlyOwner {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L25](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L25)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36)

```
File: 2022-10-blur/contracts/ExecutionDelegate.sol

36: function approveContract(address _contract) onlyOwner external {

45: function denyContract(address _contract) onlyOwner external {

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45)


```
File: 2022-10-blur/contracts/BlurExchange.sol

43:  function open() external onlyOwner {

47:  function close() external onlyOwner {

53:  function _authorizeUpgrade(address) internal override onlyOwner {}

215-217: function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner

224-226: function setPolicyManager(IPolicyManager _policyManager)
        external
        onlyOwner

233-235 : function setOracle(address _oracle)
        external
        onlyOwner

242-244: function setBlockRange(uint256 _blockRange)
        external
        onlyOwner




```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242)


[4] USING  ``PRIVATE`` RATHER THAN ``PUBLIC`` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

*There are 7 instances of this issue:*

```
File: 2022-10-blur/contracts/lib/EIP712.sol

20: bytes32 constant public FEE_TYPEHASH = keccak256(

23: bytes32 constant public ORDER_TYPEHASH = keccak256(

26: bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(

29: bytes32 constant public ROOT_TYPEHASH = keccak256(
       
```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29)


```
File: 2022-10-blur/contracts/BlurExchange.sol

57: string public constant name = "Blur Exchange";

58:  string public constant version = "1.0";

59:  uint256 public constant INVERSE_BASIS_POINT = 10000;


```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)



[5] DUPLICATED ``REQUIRE()``/``REVERT()`` CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

Saves deployment cost

*There are 4 instances of this issue:*

```
File: 2022-10-blur/contracts/ExecutionDelegate.sol

77 : require(revokedApproval[from] == false, "User has revoked approval");

92 : require(revokedApproval[from] == false, "User has revoked approval");

108 : require(revokedApproval[from] == false, "User has revoked approval");

124:  require(revokedApproval[from] == false, "User has revoked approval");

```

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124)



[6] ``ABI.ENCODE()`` IS LESS EFFICIENT THAN ``ABI.ENCODEPACKED()``

*There are 4 instances of this issue:*

```
File: 2022-10-blur/contracts/lib/EIP712.sol

44-45: return keccak256(
            abi.encode(

60-61: return keccak256(
            abi.encode(

89-91: return keccak256(
            bytes.concat(
                abi.encode(

107: abi.encode(nonce)
       
```

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L44-L45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L44-L45)


[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L60-L61](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L60-L61)


[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L89-L91](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L89-L91)


[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L107](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L107)



[7]  ``PUBLIC`` FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED ``EXTERNAL`` INSTEAD

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from ``external`` to ``public`` and can save gas by doing so.

*There are 1 instances of this issue:*

```

File: 2022-10-blur/contracts/lib/MerkleVerifier.sol

17-26:   function _verifyProof(
        bytes32 leaf,
        bytes32 root,
        bytes32[] memory proof
    ) public pure {
        bytes32 computedRoot = _computeRoot(leaf, proof);
        if (computedRoot != root) {
            revert InvalidProof();
        }
    }

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17-L26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17-L26)


[8] USE ASSEMBLY TO CHECK FOR ADDRESS(0) to save gas.

*There are 3 instances of this issue:*


```
File: 2022-10-blur/contracts/BlurExchange.sol

219: require(address(_executionDelegate) != address(0), "Address cannot be zero");

228: require(address(_policyManager) != address(0), "Address cannot be zero");

237: require(_oracle != address(0), "Address cannot be zero");

```
[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228)

[https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237)



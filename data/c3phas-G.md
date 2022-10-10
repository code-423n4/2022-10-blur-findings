## FINDINGS
NB: *Some functions have been truncated where necessary to just show affected parts of the code*
Throughout the report some places might be denoted with audit tags to show the actual place affected.

### Use 1 and 2 for Reentrancy guard(Saves  15k Gas per tx )
Using `true` and `false` will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using `1` and `2` (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier


https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L8-L20
```solidity
File: /contracts/lib/ReentrancyGuarded.sol
8: contract ReentrancyGuarded {

10:    bool reentrancyLock = false;

12:    /* Prevent a contract function from being reentrant-called. */
13:    modifier reentrancyGuard {
14:        require(!reentrancyLock, "Reentrancy detected");
15:        reentrancyLock = true;
16:        _;
17:        reentrancyLock = false;
18:    }


20: }

```

[See solmate implementation](https://github.com/transmissions11/solmate/blob/main/src/utils/ReentrancyGuard.sol)

### The result of a function call should be cached rather than re-calling the function

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L63-L82
### PolicyManager.sol.viewWhitelistedPolicies(): Results of \_whitelistedPolicies.length() should be cached instead of calling it twice
```solidity
File: /contracts/PolicyManager.sol
63:    function viewWhitelistedPolicies(uint256 cursor, uint256 size)

71:        if (length > _whitelistedPolicies.length() - cursor) { //@audit: here
72:            length = _whitelistedPolicies.length() - cursor; //@audit: here
73:        }

82:    }
```

```diff
diff --git a/contracts/PolicyManager.sol b/contracts/PolicyManager.sol
index d715ea7..f74d6f8 100644
--- a/contracts/PolicyManager.sol
+++ b/contracts/PolicyManager.sol
@@ -67,9 +67,9 @@ contract PolicyManager is IPolicyManager, Ownable {
         returns (address[] memory, uint256)
     {
         uint256 length = size;
-
-        if (length > _whitelistedPolicies.length() - cursor) {
-            length = _whitelistedPolicies.length() - cursor;
+        uint256 temp = _whitelistedPolicies.length();
+        if (length > temp - cursor) {
+            length = temp - cursor;
         }

         address[] memory whitelistedPolicies = new address[](length);
```

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

NB: *Some functions have been truncated where neccessary to just show affected parts of the code*

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L496-L515
### BlurExchange.sol.\_transferTo(): weth should be cached(Saves 1 SLOAD)
```solidity
File: /contracts/BlurExchange.sol
496:    function _transferTo(

509:        } else if (paymentToken == weth) { //@audit: weth SLOAD 1
510:            /* Transfer funds in WETH. */
511:            executionDelegate.transferERC20(weth, from, to, amount);//@audit: weth SLOAD 2

515:    }
```

### Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L278-L284
```solidity
File: /contracts/BlurExchange.sol
278:    function _canSettleOrder(uint256 listingTime, uint256 expirationTime)
279:        view
280:        internal
281:        returns (bool)
282:    {

```
The above is only called once on [Line 269](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L269)

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L344-L366
```solidity
File: /contracts/BlurExchange.sol
344:    function _validateUserAuthorization(
345:        bytes32 orderHash,
346:        address trader,
347:        uint8 v,
348:        bytes32 r,
349:        bytes32 s,
350:        SignatureVersion signatureVersion,
351:        bytes calldata extraSignature
352:    ) internal view returns (bool) {
```
The above is only called once on [Line 303](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L303)

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L375-L393
```solidity
File: /contracts/BlurExchange.sol
375:    function _validateOracleAuthorization(
376:        bytes32 orderHash,
377:        SignatureVersion signatureVersion,
378:        bytes calldata extraSignature,
379:        uint256 blockNumber
380:    ) internal view returns (bool) {
```
The above is only called once on [Line 320](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L320)

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L444-L460
```solidity
File: /contracts/BlurExchange.sol
444:    function _executeFundsTransfer(
445:        address seller,
446:        address buyer,
447:        address paymentToken,
448:        Fee[] calldata fees,
449:        uint256 price
450:    ) internal {
```
The above is only called once on [Line 147](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L147)

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L469-L487
```solidity
File: /contracts/BlurExchange.sol
469:    function _transferFees(
470:        Fee[] calldata fees,
471:        address paymentToken,
472:        address from,
473:        uint256 price
474:    ) internal returns (uint256) {
```
The above is only called once on [Line 456](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L456)

### Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L181-L192
### BlurExchange.sol.cancelOrder(): cancelledOrFilled[hash] should be cached
```solidity
File: /contracts/BlurExchange.sol
181:    function cancelOrder(Order calldata order) public {

187:        if (!cancelledOrFilled[hash]) { //@audit: cancelledOrFilled[hash]
188:            /* Mark order as cancelled, preventing it from being matched. */
189:            cancelledOrFilled[hash] = true; //@audit: cancelledOrFilled[hash]
190:            emit OrderCancelled(hash);
191:        }
192:    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L207-L210
### BlurExchange.sol.incrementNonce(): nonces[msg.sender] should be cached
```solidity
File: /contracts/BlurExchange.sol
207:    function incrementNonce() external {
208:        nonces[msg.sender] += 1; //@audit: nonces[msg.sender]
209:        emit NonceIncremented(msg.sender, nonces[msg.sender]); //@audit: nonces[msg.sender]
210:    }
```
### Use calldata instead of memory for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

Note that I've also flagged instances where the function is public but can be marked as external since it's not called by the contract.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17-L26
```solidity
File: /contracts/lib/MerkleVerifier.sol

//@audit: Use calldata on bytes32[] memory proof
17:    function _verifyProof(
18:        bytes32 leaf,
19:        bytes32 root,
20:        bytes32[] memory proof
21:    ) public pure {
22:        bytes32 computedRoot = _computeRoot(leaf, proof);
23:        if (computedRoot != root) {
24:            revert InvalidProof();
25:        }
26:    }
```

### Emitting storage values instead of the memory one.
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L215-L222
```solidity
File: /contracts/BlurExchange.sol
//@audit: We should emit _executionDelegate instead of executionDelegate
215:    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
216:        external
217:        onlyOwner
218:    {
219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
220:        executionDelegate = _executionDelegate;
221:        emit NewExecutionDelegate(executionDelegate);
222:    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L224-L231
```solidity
File: /contracts/BlurExchange.sol

//@audit: We should emit _policyManager instead of policyManager 
224:    function setPolicyManager(IPolicyManager _policyManager)
225:        external
226:        onlyOwner
227:    {
228:        require(address(_policyManager) != address(0), "Address cannot be zero");
229:        policyManager = _policyManager;
230:        emit NewPolicyManager(policyManager);
231:    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L233-L240
```solidity
File: /contracts/BlurExchange.sol

//@audit: We should emit _oracle instead of oracle
233:    function setOracle(address _oracle)
234:        external
235:        onlyOwner
236:    {
237:        require(_oracle != address(0), "Address cannot be zero");
238:        oracle = _oracle;
239:        emit NewOracle(oracle);
240:    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L242-L248
```solidity
File: /contracts/BlurExchange.sol

//@audit: We should emit _blockRange instead of blockRange
242:    function setBlockRange(uint256 _blockRange)
243:        external
244:        onlyOwner
245:    {
246:        blockRange = _blockRange;
247:        emit NewBlockRange(blockRange);
248:    }
```

### x += y costs more gas than x = x + y for state variables
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208
```solidity
File: /contracts/BlurExchange.sol
208:        nonces[msg.sender] += 1;
```
### Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.


**consequences:**
-  Each usage of a "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..)

-  Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library )
 
See: ethereum/solidity#9232

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L35
```solidity
File: /contracts/lib/EIP712.sol
20:    bytes32 constant public FEE_TYPEHASH = keccak256(
21:        "Fee(uint16 rate,address recipient)"
22:    );


23:    bytes32 constant public ORDER_TYPEHASH = keccak256(
24:        "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
25:    );


26:    bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
27:        "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
28:    );

29:    bytes32 constant public ROOT_TYPEHASH = keccak256(
30:        "Root(bytes32 root)"
31:    );


33:    bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
34:        "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
35:    );
```

**Proof: can be tested on remix also, showing results for foundry** 
```solidity
pragma solidity ^0.8.6;
contract Constant{
    bytes32 constant noteDenom = keccak256(bytes("NOTE"));
    function doConstant() external view returns(bytes32){
        return noteDenom;
    }
}
contract Immutable{
    bytes32 immutable noteDenom = keccak256(bytes("NOTE"));
    function doImmutable() external view returns(bytes32){
        return noteDenom;
    }
}
```

**Results from :** `forge test --gas-report --optimize`

```solidity
Running 1 test for test/Constant.t.sol:ConstantTest
[PASS] testdoThing() (gas: 5416)
Test result: ok. 1 passed; 0 failed; finished in 420.08µs

Running 1 test for test/Immutable.t.sol:ImmutableTest
[PASS] testdoThing() (gas: 5356)
Test result: ok. 1 passed; 0 failed; finished in 389.91µs
```


### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L485
```solidity
File: /contracts/BlurExchange.sol
485:        uint256 receiveAmount = price - totalFee;
```

The above operation cannot underflow due to the check on [Line 482](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482) that ensures that `price` is greater than `totalFee` before performing the subtraction.

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77

```solidity
File: /contracts/PolicyManager.sol
77:        for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
```solidity
File: /contracts/lib/EIP712.sol
77:        for (uint256 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
```solidity
File: /contracts/BlurExchange.sol
199:        for (uint8 i = 0; i < orders.length; i++) {

476:        for (uint8 i = 0; i < fees.length; i++) {

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38
```solidity
File: /contracts/lib/MerkleVerifier.sol
38:        for (uint256 i = 0; i < proof.length; i++) {
```
[see resource](https://github.com/ethereum/solidity/issues/10695)

### Cache the length of arrays in loops (saves ~6 gas per iteration)
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

The solidity compiler will always read the length of the array during each iteration. That is,

   1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
   2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
   3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):
 When reading the length of an array,  **sload** or **mload** or **calldataload** operation is only called once and subsequently replaced by a cheap **dupN** instruction. Even though mload , calldataload and dupN have the same gas cost, mload and calldataload needs an additional dupN to put the offset in the stack, i.e., an extra 3 gas. which brings this to 6 gas
 
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
```solidity
File: /contracts/lib/EIP712.sol
77:        for (uint256 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
```solidity
File: /contracts/BlurExchange.sol
199:        for (uint8 i = 0; i < orders.length; i++) {

476:        for (uint8 i = 0; i < fees.length; i++) {

```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38
```solidity
File: /contracts/lib/MerkleVerifier.sol
38:        for (uint256 i = 0; i < proof.length; i++) {
```
### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77

```solidity
File: /contracts/PolicyManager.sol
77:        for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
```solidity
File: /contracts/lib/EIP712.sol
77:        for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
```solidity
File: /contracts/BlurExchange.sol
199:        for (uint8 i = 0; i < orders.length; i++) {

476:        for (uint8 i = 0; i < fees.length; i++) {

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38
```solidity
File: /contracts/lib/MerkleVerifier.sol
38:        for (uint256 i = 0; i < proof.length; i++) {
```

### Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77
```solidity
File: /contracts/ExecutionDelegate.sol
77:        require(revokedApproval[from] == false, "User has revoked approval");

92:        require(revokedApproval[from] == false, "User has revoked approval");

108:        require(revokedApproval[from] == false, "User has revoked approval");

124:        require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L267
```solidity
File: /contracts/BlurExchange.sol
267:            (cancelledOrFilled[orderHash] == false) &&
```
### Using bools for storage incurs overhead
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L18-L19
```solidity
File: /contracts/ExecutionDelegate.sol
18:    mapping(address => bool) public contracts;
19:    mapping(address => bool) public revokedApproval;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L71
```solidity
File: /contracts/BlurExchange.sol
71:    mapping(bytes32 => bool) public cancelledOrFilled;
```
### A modifier used only once and not being inherited should be inlined to save gas
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L35-L38
```solidity
File: /contracts/BlurExchange.sol
35:    modifier whenOpen() {
36:        require(isOpen == 1, "Closed");
37:        _;
38:    }
```
The above modifier is only used once on [Line 132](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L132)

### Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57-L59
```solidity
File: /contracts/BlurExchange.sol
57:    string public constant name = "Blur Exchange";
58:    string public constant version = "1.0";
59:    uint256 public constant INVERSE_BASIS_POINT = 10000;
```


### Use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22
```solidity
File: /contracts/ExecutionDelegate.sol
22~:        require(contracts[msg.sender], "Contract is not approved to make transfers");
```
**Other instances to modify**
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482
```solidity
File: /contracts/BlurExchange.sol
482:        require(totalFee <= price, "Total amount of fees are more than the price");
```

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.

### Use Custom Errors instead of Revert Strings to save Gas(~50 gas)
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26
```solidity
File: /contracts/PolicyManager.sol
26:        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

37:        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22
```solidity
File: /contracts/ExecutionDelegate.sol
22:        require(contracts[msg.sender], "Contract is not approved to make transfers");

77:        require(revokedApproval[from] == false, "User has revoked approval");

92:        require(revokedApproval[from] == false, "User has revoked approval");

108:       require(revokedApproval[from] == false, "User has revoked approval");

124:       require(revokedApproval[from] == false, "User has revoked approval");
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134
```solidity
File: /contracts/BlurExchange.sol
134:        require(sell.order.side == Side.Sell);

139:        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140:        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");


142:        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143:        require(_validateSignatures(buy, buyHash), "Buy failed authorization");

219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");

228:        require(address(_policyManager) != address(0), "Address cannot be zero");

237:        require(_oracle != address(0), "Address cannot be zero");

318:            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");

407:        require(v == 27 || v == 28, "Invalid v parameter");

424:            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");

428:            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");

431:        require(canMatch, "Orders cannot be matched");

482:        require(totalFee <= price, "Total amount of fees are more than the price");

534:        require(_exists(collection), "Collection does not exist");
```

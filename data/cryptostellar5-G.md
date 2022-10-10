### G-01 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED


*Number of Instances Identified: 7*

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with `for (uint256 i; i < numIterations; ++i) {`

Consider removing explicit initializations for default values.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
199: for (uint8 i = 0; i < orders.length; i++) {
475: uint256 totalFee = 0;
476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

```
for (uint256 i = 0; i < length; i++)
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

```
for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10

```
bool reentrancyLock = false;
```


### G-02 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW

*Number of Instances Identified: 5*

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

```
for (uint256 i = 0; i < length; i++)
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

```
for (uint256 i = 0; i < proof.length; i++) {
```


### G-03 ARRAY.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR- LOOP

*Number of Instances Identified: 3*

Reading array length at each iteration of the loop consumes more gas than necessary.

In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Here, Consider storing the array’s length in a variable before the for-loop, and use this new variable instead.


https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38


```
for (uint256 i = 0; i < proof.length; i++) {
```


### G-04 ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 (SAME FOR --I VS I-- OR I -= 1)

*Number of Instances Identified: 5*

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

-   `i += 1` is the most expensive form
-   `i++` costs 6 gas less than `i += 1`
-   `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

-   `i -= 1` is the most expensive form
-   `i--` costs 11 gas less than `i -= 1`
-   `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name _post-increment_:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

```
for (uint256 i = 0; i < length; i++)
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

```
for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

```
for (uint256 i = 0; i < proof.length; i++) {
```


### G-05 FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

*Number of Instances Identified: 11*

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
43: function open() external onlyOwner
47: function close() external onlyOwner
53: function _authorizeUpgrade(address) internal override onlyOwner
215:  function setExecutionDelegate(IExecutionDelegate _executionDelegate) external onlyOwner
224: function setPolicyManager(IPolicyManager _policyManager) external onlyOwner
233: function setOracle(address _oracle) external onlyOwner
242:  function setBlockRange(uint256 _blockRange) external onlyOwner
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

```
25: function addPolicy(address policy) external override onlyOwner
36: function removePolicy(address policy) external override onlyOwner
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
36: function approveContract(address _contract) onlyOwner external
45: function denyContract(address _contract) onlyOwner external
```


### G-06 USING BOOLS FOR STORAGE INCURS OVERHEAD

*Number of Instances Identified: 8*

```
    // Booleans are more expensive than uint256 or any type that takes up a full    // word because each write operation emits an extra SLOAD to first read the    // slot's contents, replace the bits taken up by the boolean, and then write    // back. This is the compiler's defense against contract upgrades and    // pointer aliasing, and it cannot be disabled.
```

[https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
164: cancelledOrFilled[sellHash] = true;
165: cancelledOrFilled[buyHash] = true;
189: cancelledOrFilled[hash] = true;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
37: contracts[_contract] = true;
46: contracts[_contract] = false;
54: revokedApproval[msg.sender] = true;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol

```
15: reentrancyLock = true;
17: reentrancyLock = false;
```


### G-07 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Number of Instances Identified: 9*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed


https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
199: for (uint8 i = 0; i < orders.length; i++) {
347: uint8 v
383: uint8 v
385: uint8
388: uint8 _v
403: uint8 v
476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol

```
9: uint16 rate
32: uint8 v
```

### G-08 USE CUSTOM ERRORSUSE CUSTOM ERRORS RATHER THAN REVERT() or REQUIRE() STRINGS TO SAVE GAS

*Number of Instances Identified: 24*

Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they’re hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
36:  require(isOpen == 1, "Closed");
139: require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140: require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
142: require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143: require(_validateSignatures(buy, buyHash), "Buy failed authorization");
219: require(address(_executionDelegate) != address(0), "Address cannot be zero");
228: require(address(_policyManager) != address(0), "Address cannot be zero");
237: require(_oracle != address(0), "Address cannot be zero");
318: require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
407: require(v == 27 || v == 28, "Invalid v parameter");
424: require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
428: require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
431: require(canMatch, "Orders cannot be matched");
482: require(totalFee <= price, "Total amount of fees are more than the price");
513: revert("Invalid payment token");
534: require(_exists(collection), "Collection does not exist");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
22: require(contracts[msg.sender], "Contract is not approved to make transfers");
77: require(revokedApproval[from] == false, "User has revoked approval");
92: require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol


```
26: require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
37: require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14

```
14: require(!reentrancyLock, "Reentrancy detected");
```


### G-09 REQUIRE or REVERT STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

*Number of Instances Identified: 2*

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

```
require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#22

```
require(contracts[msg.sender], "Contract is not approved to make transfers");
```


### G-10 Using both named returns and a return statement isnt necessary

*Number of Instances Identified: 2*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L416-L434

```
    function _canMatchOrders(Order calldata sell, Order calldata buy) 
    function
        internal
        view
        returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType) 
    {
        bool canMatch;
        if (sell.listingTime <= buy.listingTime) {
            /* Seller is maker. */
            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy).canMatchMakerAsk(sell, buy); 
        } else {
            /* Buyer is maker. */
            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy).canMatchMakerBid(buy, sell); 
        }
        require(canMatch, "Orders cannot be matched"); 

        return (price, tokenId, amount, assetType);
    }
```


https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L29-L31

```
    function _implementation() internal view virtual override returns (address impl) { 
        return ERC1967Upgrade._getImplementation();
    }
```


### G-11 EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

*Number of Instances Identified: 1*

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53

```
function _authorizeUpgrade(address) internal override onlyOwner {}
```

### G-12 INLINE A MODIFIER THAT IS USED ONLY ONCE


*Number of Instances Identified: 1*

```
 modifier whenOpen() {
        require(isOpen == 1, "Closed");
        _;
    }
```

Modifier  `whenOpen` is only used once  in `execute()` function. This should instead be inlined to save gas

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53


### G-13 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 2*

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
208: nonces[msg.sender] += 1;
479: totalFee += fee;
```



### G-14 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 11*

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
278: function _canSettleOrder
344: function _validateUserAuthorization()
375: function _validateOracleAuthorization
416: function _canMatchOrders()
444: function _executeFundsTransfer()
469: function _transferFees()
525: function _executeTokenTransfer()
548: function _exists()
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```
39: function _hashDomain()
55: function _hashFee()
69: function _packFees()
```



### G-15 DONT COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

*Number of Instances Identified: 5*

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
267: (cancelledOrFilled[orderHash] == false)
```


https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
77: require(revokedApproval[from] == false, "User has revoked approval")
92: require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```


### G-16 DUPLICATED REQUIRE() or REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

*Number of Instances Identified: 4*

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
77: require(revokedApproval[from] == false, "User has revoked approval");
92: require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```
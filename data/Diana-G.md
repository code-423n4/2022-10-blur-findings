## G-01 ++i costs less gas than i++ especially when it's used in for loops (Same applies for --i , i-- too)

Saves 6 gas _PER LOOP_

Prefix increments are cheaper than postfix increments. 

Further more, using unchecked {++i} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change  

### Proof of Concept

_There are 5 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

```
File: contracts/PolicyManager.sol

77: for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```
File: contracts/lib/EIP712.sol

77: for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

```
File: contracts/lib/MerkleVerifier.sol

38: for (uint256 i = 0; i < proof.length; i++) {
```

----------------

## G-02 An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

The overheads outlined below are _PER LOOP_, excluding the first loop

+ storage arrays incur a Gwarmaccess (100 gas)
+ memory arrays use `MLOAD` (3 gas)
+ calldata arrays use `CALLDATALOAD` (3 gas)

Caching the array length in the stack saves around 3 gas per iteration.

It is suggested to store the array’s length in a variable before the for-loop, and use it instead

### Proof of Concept

_There are 4 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

199: for (uint8 i = 0; i < orders.length; i++) {
476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```
File: contracts/lib/EIP712.sol

77: for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

```
File: contracts/lib/MerkleVerifier.sol

38: for (uint256 i = 0; i < proof.length; i++) {
```

--------------

## G-03 Don't compare boolean expressions to boolean literals

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value. I suggest using `if(!directValue)` instead of `if(directValue == false)`

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

### Proof of Concept

_There are 5 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

267: (cancelledOrFilled[orderHash] == false) &&
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
File: contracts/ExecutionDelegate.sol

77:  require(revokedApproval[from] == false, "User has revoked approval");
92:  require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```

------

## G-04 Duplicated require() or revert() checks should be refactored to a modifier or function

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
File: contracts/ExecutionDelegate.sol

77:  require(revokedApproval[from] == false, "User has revoked approval");
92:  require(revokedApproval[from] == false, "User has revoked approval");
108: require(revokedApproval[from] == false, "User has revoked approval");
124: require(revokedApproval[from] == false, "User has revoked approval");
```

------------

## G-05 Empty blocks should be removed or emit something

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

53: function _authorizeUpgrade(address) internal override onlyOwner {}
```

------------

## G-06 Expressions for constant values such as a call to keccak256() should use immutable rather than constant

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20-L35

```
File: contracts/lib/EIP712.sol

bytes32 constant public FEE_TYPEHASH = keccak256(

"Fee(uint16 rate,address recipient)"

);

bytes32 constant public ORDER_TYPEHASH = keccak256(

"Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"

);

bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(

"OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"

);

bytes32 constant public ROOT_TYPEHASH = keccak256(

"Root(bytes32 root)"

);

bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(

"EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"

);
```

---------------

## G-07 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Proof of Concept

_There are 11 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

43:      function open() external onlyOwner {

47:      function close() external onlyOwner {

53:      function _authorizeUpgrade(address) internal override onlyOwner {}

215-217: function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner

224-226: function setPolicyManager(IPolicyManager _policyManager)
        external
        onlyOwner

233-235: function setOracle(address _oracle)
        external
        onlyOwner

242-244: function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
File: contracts/ExecutionDelegate.sol

36: function approveContract(address _contract) onlyOwner external {

45: function denyContract(address _contract) onlyOwner external {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

```
File: contracts/PolicyManager.sol

25: function addPolicy(address policy) external override onlyOwner {

36: function removePolicy(address policy) external override onlyOwner {
```

----------

## G-08 Increments can be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

### Proof of Concept

_There are 4 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

199: for (uint8 i = 0; i < orders.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

```
File: contracts/PolicyManager.sol

77: for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```
File: contracts/lib/EIP712.sol

77: for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

```
File: contracts/lib/MerkleVerifier.sol

38: for (uint256 i = 0; i < proof.length; i++) {
```

-----------

## G-09 Internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

### Proof of Concept

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol

```
File: contracts/lib/ERC1967Proxy.sol

29: function _implementation() internal view virtual override returns (address impl) {
```

------------

## G-10 Internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Proof of Concept

_There are 14 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

278-281: function _canSettleOrder(uint256 listingTime, uint256 expirationTime)
        view
        internal
        returns (bool)
        
344-352: function _validateUserAuthorization(
        bytes32 orderHash,
        address trader,
        uint8 v,
        bytes32 r,
        bytes32 s,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature
    ) internal view returns (bool) {
    
375-380: function _validateOracleAuthorization(
        bytes32 orderHash,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature,
        uint256 blockNumber
    ) internal view returns (bool) {
    
416-419: function _canMatchOrders(Order calldata sell, Order calldata buy)
        internal
        view
        returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
        
444-450: function _executeFundsTransfer(
        address seller,
        address buyer,
        address paymentToken,
        Fee[] calldata fees,
        uint256 price
    ) internal {
    
469-474: function _transferFees(
        Fee[] calldata fees,
        address paymentToken,
        address from,
        uint256 price
    ) internal returns (uint256) {
    
525-532: function _executeTokenTransfer(
        address collection,
        address from,
        address to,
        uint256 tokenId,
        uint256 amount,
        AssetType assetType
    ) internal {
    
548-551: function _exists(address what)
        internal
        view
        returns (bool)
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```
File: contracts/lib/EIP712.sol

39-42:   function _hashDomain(EIP712Domain memory eip712Domain)
         internal
         pure
         returns (bytes32)
        
55-58:   function _hashFee(Fee calldata fee)
         internal 
         pure
         returns (bytes32)
        
69-72:   function _packFees(Fee[] calldata fees)
         internal
         pure
         returns (bytes32)
        
112-115: function _hashToSign(bytes32 orderHash)
         internal
         view
         returns (bytes32 hash)
         
124-127: function _hashToSignRoot(bytes32 root)
         internal
         view
         returns (bytes32 hash)
         
139-142: function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
         internal
         view
         returns (bytes32 hash)
```

---------

## G-11 Require() or revert() strings longer than 32 bytes cost extra gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

I suggest shortening the revert strings to fit in 32 bytes, or that using custom errors as described next.

### Proof of Concept

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

482: require(totalFee <= price, "Total amount of fees are more than the price");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol

```
File: contracts/ExecutionDelegate.sol

22: require(contracts[msg.sender], "Contract is not approved to make transfers");
```

--------

## G-12 Usage of uints or ints smaller than 32 bytes (26 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

### Proof of Concept

_There are 9 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

199: for (uint8 i = 0; i < orders.length; i++) {

347: uint8 v,

383: uint8 v; bytes32 r; bytes32 s;

385: (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));

388: (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = 
abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));

403: uint8 v,

476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol

```
File: contracts/lib/OrderStructs.sol

9:  uint16 rate;

32: uint8 v;
```

----------

## G-13 Use custom errors rather than revert() or require() strings to save deployment gas

Custom errors are available from solidity version 0.8.4. The instances below match or exceed that version

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Source: [https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/):

> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the `error` statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Proof of Concept

_There are 24 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

36:  require(isOpen == 1, "Closed");

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
File: contracts/ExecutionDelegate.sol

22:  require(contracts[msg.sender], "Contract is not approved to make transfers");

77:  require(revokedApproval[from] == false, "User has revoked approval");

92:  require(revokedApproval[from] == false, "User has revoked approval");

108: require(revokedApproval[from] == false, "User has revoked approval");

124: require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

```
File: contracts/PolicyManager.sol

26: require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

37: require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol

```
File: contracts/lib/ReentrancyGuarded.sol

14: require(!reentrancyLock, "Reentrancy detected");
```

----------

## G-14 x += y costs more gas than x = x+y for state variables

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

208: nonces[msg.sender] += 1;

479: totalFee += fee;
```

-----

## G-15 It costs more gas to initialize variables to zero than to let the default of zero be applied 

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

_There are 7 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```
File: contracts/BlurExchange.sol

199: for (uint8 i = 0; i < orders.length; i++) {

475: uint256 totalFee = 0;

476: for (uint8 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol

```
File: contracts/PolicyManager.sol

77: for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```
File: contracts/lib/EIP712.sol

77: for (uint256 i = 0; i < fees.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

```
File: contracts/lib/MerkleVerifier.sol

38: for (uint256 i = 0; i < proof.length; i++) {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol

```
File: contracts/lib/ReentrancyGuarded.sol

10: bool reentrancyLock = false;
```

--------

## G-16 Using both named returns and a return statement isn’t necessary

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L416-L434

```
File: contracts/BlurExchange.sol

function _canMatchOrders(Order calldata sell, Order calldata buy)

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
--------

## G-17 Not using the named return variables when a function returns, wastes deployment gas

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol

```
File: contracts/lib/EIP712.sol

124-137: function _hashToSignRoot(bytes32 root)
        internal
        view
        returns (bytes32 hash)
    {
        return keccak256(abi.encodePacked(
            "\x19\x01",
            DOMAIN_SEPARATOR,
            keccak256(abi.encode(
                ROOT_TYPEHASH,
                root
            ))
        ));
    }

139-153: function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
        internal
        view
        returns (bytes32 hash)
    {
        return keccak256(abi.encodePacked(
            "\x19\x01",
            DOMAIN_SEPARATOR,
            keccak256(abi.encode(
                ORACLE_ORDER_TYPEHASH,
                orderHash,
                blockNumber
            ))
        ));
    }
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol

```
File: contracts/lib/ERC1967Proxy.sol

29-31: function _implementation() internal view virtual override returns (address impl) {

return ERC1967Upgrade._getImplementation();

}
```

---------
## G-18 Inline a modifier that is only used once

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L128-L132

```
File: contracts/BlurExchange.sol

function execute(Input calldata sell, Input calldata buy)
       external
       payable
       reentrancyGuard
       whenOpen
```

-----

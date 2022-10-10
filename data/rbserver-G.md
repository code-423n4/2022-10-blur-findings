## [G-01] MEMORY VARIABLES CAN BE USED INSTEAD OF STATE VARIABLES IF POSSIBLE
Accessing a memory variable instead of a state variable replaces an `sload` operation with an `mload` operation. When this is possible, gas is saved.

In the following `setExecutionDelegate` function, `_executionDelegate` can be used instead of `executionDelegate` for emitting the `NewExecutionDelegate` event.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215-L222
```solidity
    function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(executionDelegate);
    }
```

In the following `setOracle` function, `_oracle` can be used instead of `oracle` for emitting the `NewOracle` event.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233-L240
```solidity
    function setOracle(address _oracle)
        external
        onlyOwner
    {
        require(_oracle != address(0), "Address cannot be zero");
        oracle = _oracle;
        emit NewOracle(oracle);
    }
```

In the following `setBlockRange` function, `_blockRange` can be used instead of `blockRange` for emitting the `NewBlockRange` event.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242-L248
```solidity
    function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {
        blockRange = _blockRange;
        emit NewBlockRange(blockRange);
    }
```

## [G-02] UNUSED VARIABLE DOES NOT NEED TO BE STORED IN MEMORY
In the following `_validateOracleAuthorization` function, `( , uint8 _v, bytes32 _r, bytes32 _s)` can be implemented instead of `(bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s)` because `merklePath` is not used; storing the unused `merklePath` in memory wastes gas.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L375-L393
```solidity
    function _validateOracleAuthorization(
        bytes32 orderHash,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature,
        uint256 blockNumber
    ) internal view returns (bool) {
        bytes32 oracleHash = _hashToSignOracle(orderHash, blockNumber);

        uint8 v; bytes32 r; bytes32 s;
        if (signatureVersion == SignatureVersion.Single) {
            (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
        } else if (signatureVersion == SignatureVersion.Bulk) {
            /* If the signature was a bulk listing the merkle path musted be unpacked before the oracle signature. */
            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
            v = _v; r = _r; s = _s;
        }

        return _recover(oracleHash, v, r, s) == oracle;
    }
```

## [G-03] BOOLEAN EXPRESSIONS CAN BE USED INSTEAD OF COMPARING BOOLEAN VALUES TO BOOLEAN LITERALS
Comparing a boolean value to a boolean literal needs the `iszero` operation and costs more gas than using a boolean expression.

`!cancelledOrFilled[orderHash]` can be used instead of `cancelledOrFilled[orderHash] == false` in the following code.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L267
```solidity
            (cancelledOrFilled[orderHash] == false) &&
```

`!revokedApproval[from]` can be used instead of `revokedApproval[from] == false` in the following lines of code.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

## [G-04] SAME `require` STATEMENTS CAN BE REFACTORED TO A MODIFIER FOR RELEVANT FUNCTIONS
The same `require` statements can be refactored to a modifier, which can then be used in the relevant functions. This can make deployment cheaper.

Please consider refactoring `require(revokedApproval[from] == false, "User has revoked approval")` to a modifier for being used in the `transferERC721Unsafe`, `transferERC721`, `transferERC1155`, and `transferERC20` functions that include the following lines of code.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
```solidity
        require(revokedApproval[from] == false, "User has revoked approval");
```

## [G-05] ARITHMETIC OPERATION THAT DOES NOT UNDERFLOW CAN BE UNCHECKED
Explicitly unchecking the arithmetic operation that does not underflow by wrapping it in `unchecked {}` costs less gas than implicitly checking it.

`price - totalFee` can be unchecked in the following code because `totalFee <= price` is required already.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482-L485
```solidity
        require(totalFee <= price, "Total amount of fees are more than the price");

        /* Amount that will be received by seller. */
        uint256 receiveAmount = price - totalFee;
```

## [G-06] ARITHMETIC OPERATIONS THAT DO NOT OVERFLOW CAN BE UNCHECKED
Explicitly unchecking arithmetic operations that do not overflow by wrapping these in `unchecked {}` costs less gas than implicitly checking these.

For the following loops, `unchecked {++i}` at the end of the loop block can be implemented instead of `i++`.
```solidity
contracts\BlurExchange.sol
  199: for (uint8 i = 0; i < orders.length; i++) {
  476: for (uint8 i = 0; i < fees.length; i++) {

contracts\PolicyManager.sol
  77: for (uint256 i = 0; i < length; i++) {

contracts\lib\EIP712.sol
  77: for (uint256 i = 0; i < fees.length; i++) {

contracts\lib\MerkleVerifier.sol
  38: for (uint256 i = 0; i < proof.length; i++) {
```

## [G-07] ARRAY LENGTH CAN BE CACHED OUTSIDE OF LOOP
Caching the array length outside of the loop and using the cached length in the loop costs less gas than reading the array length for each iteration. For example, `fees.length` in the following code can be cached outside of the loop like `uint256 feesLength = fees.length`, and `i < feesLength` can be used for each iteration.
```solidity
contracts\BlurExchange.sol
  199: for (uint8 i = 0; i < orders.length; i++) {
  476: for (uint8 i = 0; i < fees.length; i++) {

contracts\lib\EIP712.sol
  77: for (uint256 i = 0; i < fees.length; i++) {

contracts\lib\MerkleVerifier.sol
  38: for (uint256 i = 0; i < proof.length; i++) {
```

## [G-08] VARIABLE DOES NOT NEED TO BE INITIALIZED TO ITS DEFAULT VALUE
Explicitly initializing a variable with its default value costs more gas than uninitializing it. For example, `uint256 i` can be used instead of `uint256 i = 0` in the following code.
```solidity
contracts\BlurExchange.sol
  199: for (uint8 i = 0; i < orders.length; i++) {
  476: for (uint8 i = 0; i < fees.length; i++) {

contracts\PolicyManager.sol
  77: for (uint256 i = 0; i < length; i++) {

contracts\lib\EIP712.sol
  77: for (uint256 i = 0; i < fees.length; i++) {

contracts\lib\MerkleVerifier.sol
  38: for (uint256 i = 0; i < proof.length; i++) {
```

## [G-09] ++VARIABLE CAN BE USED INSTEAD OF VARIABLE++
++variable costs less gas than variable++. For example, `i++` can be changed to `++i` for the following loops.
```solidity
contracts\BlurExchange.sol
  199: for (uint8 i = 0; i < orders.length; i++) {
  476: for (uint8 i = 0; i < fees.length; i++) {

contracts\PolicyManager.sol
  77: for (uint256 i = 0; i < length; i++) {

contracts\lib\EIP712.sol
  77: for (uint256 i = 0; i < fees.length; i++) {

contracts\lib\MerkleVerifier.sol
  38: for (uint256 i = 0; i < proof.length; i++) {
```

## [G-10] X = X + Y CAN BE USED INSTEAD OF X += Y
x = x + y costs less gas than x += y. For example, `totalFee += fee` can be changed to `totalFee = totalFee + fee` in the following code.
```solidity
contracts\BlurExchange.sol
  208: nonces[msg.sender] += 1;
  479: totalFee += fee;
```

## [G-11] `require` REASON STRINGS CAN BE SHORTENED TO FIT IN 32 BYTES
`require` reason strings that are longer than 32 bytes need more memory space and more `mstore` operations than these that are shorter than 32 bytes when reverting. Please consider shortening the following `require` reason strings.
```solidity
contracts\BlurExchange.sol
  482: require(totalFee <= price, "Total amount of fees are more than the price");

contracts\ExecutionDelegate.sol
  22: require(contracts[msg.sender], "Contract is not approved to make transfers");
```

## [G-12] `revert` WITH CUSTOM ERROR CAN BE USED INSTEAD OF `require` WITH REASON STRING
`revert` with custom error can cost less gas than `require` with reason string. Please consider using `revert` with custom error to replace the following `require`.
```solidity
contracts\BlurExchange.sol
  36: require(isOpen == 1, "Closed");
  134: require(sell.order.side == Side.Sell);
  139: require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
  140: require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
  142: require(_validateSignatures(sell, sellHash), "Sell failed authorization");
  143: require(_validateSignatures(buy, buyHash), "Buy failed authorization");
  183: require(msg.sender == order.trader);
  219: require(address(_executionDelegate) != address(0), "Address cannot be zero");
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

contracts\ExecutionDelegate.sol
  22: require(contracts[msg.sender], "Contract is not approved to make transfers");   
  77: require(revokedApproval[from] == false, "User has revoked approval");
  92: require(revokedApproval[from] == false, "User has revoked approval");
  108: require(revokedApproval[from] == false, "User has revoked approval");
  124: require(revokedApproval[from] == false, "User has revoked approval");

contracts\PolicyManager.sol
  26: require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
  37: require(_whitelistedPolicies.contains(policy), "Not whitelisted");

contracts\lib\ReentrancyGuarded.sol
  14: require(!reentrancyLock, "Reentrancy detected");
```
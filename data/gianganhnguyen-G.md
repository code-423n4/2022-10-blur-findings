# 1. [G-1] For loops: ++i  cost less gas compare to i++

Instances include:

    File contracts/BlurExchange.sol, line 99:       for (uint8 i = 0; i < orders.length; i++)
    File contracts/BlurExchange.sol, line 476:     for (uint8 i = 0; i < fees.length; i++) {
    File contracts/PolicyManager.sol, line 77:     for (uint256 i = 0; i < length; i++)
    File contracts/lib/EIP712.sol, line 77:             for (uint256 i = 0; i < fees.length; i++) {
    File contracts/lib/MerkleVerifier.sol, line 77: for (uint256 i = 0; i < proof.length; i++) {

I suggest using `++i` instead of `i++` to increment the value of an uint variable.

# 2. [G-2] For loops: Cache the length of arrays in the loops to save gas

    File contracts/BlurExchange.sol, line 99:       for (uint8 i = 0; i < orders.length; i++)
    File contracts/BlurExchange.sol, line 476:     for (uint8 i = 0; i < fees.length; i++) {
    File contracts/PolicyManager.sol, line 77:     for (uint256 i = 0; i < length; i++)
    File contracts/lib/EIP712.sol, line 77:             for (uint256 i = 0; i < fees.length; i++) {
    File contracts/lib/MerkleVerifier.sol, line 77: for (uint256 i = 0; i < proof.length; i++) {

I suggest storing the array’s length in a variable before the for-loop, and use it instead.

# 3. [G-3] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File contracts/PolicyManager.sol, line 77:     for (uint256 i = 0; i < length; i++)
    File contracts/lib/EIP712.sol, line 77:             for (uint256 i = 0; i < fees.length; i++) {
    File contracts/lib/MerkleVerifier.sol, line 77: for (uint256 i = 0; i < proof.length; i++) {

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    for (uint256 i; i < numIterations;) { 
      ...
      unchecked { ++i; }  
    }

# 4. [G-4] Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Instances include:

    File contracts/BlurExchange.sol, line 99:       for (uint8 i = 0; i < orders.length; i++)
    File contracts/BlurExchange.sol, line 476:     for (uint8 i = 0; i < fees.length; i++) {
    File contracts/PolicyManager.sol, line 77:     for (uint256 i = 0; i < length; i++)
    File contracts/lib/EIP712.sol, line 77:             for (uint256 i = 0; i < fees.length; i++) {
    File contracts/lib/MerkleVerifier.sol, line 77: for (uint256 i = 0; i < proof.length; i++) {

I suggest removing explicit initializations for default values.

# 5. [G-5] Variables: "Constant" expressions are expressions, not constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

Instances include:

    File contracts/lib/EIP712.sol, line 20:             bytes32 constant public FEE_TYPEHASH = keccak256(
                                                                                       "Fee(uint16 rate,address recipient)"
                                                                          );
    File contracts/lib/EIP712.sol, line 23:             bytes32 constant public FEE_TYPEHASH = keccak256(
                                                                                       "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
                                                                          );
    File contracts/lib/EIP712.sol, line 26:             bytes32 constant public FEE_TYPEHASH = keccak256(
                                                                                       "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
                                                                          );
    File contracts/lib/EIP712.sol, line 29:             bytes32 constant public FEE_TYPEHASH = keccak256(
                                                                                       "Root(bytes32 root)"
                                                                          );
    File contracts/lib/EIP712.sol, line 33:             bytes32 constant public FEE_TYPEHASH = keccak256(
                                                                                       "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
                                                                          );

Change these expressions from `constant` to `immutable` and implement the calculation in the constructor, or hardcode these values in the constants and add a comment to say how the value was calculated.

# 6. [G-6] Comparisons: Boolean comparations

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned value boolean. I suggest using `require (condition, ...)` instead of `require (condition == true, ...)` and `require (!condition, ...)` instead of `require (condition == false, ...)` here:

    File contracts/ExecutionDelegate.sol, line 77:     require(revokedApproval[from] == false, "User has revoked approval");
    File contracts/ExecutionDelegate.sol, line 92:     require(revokedApproval[from] == false, "User has revoked approval");
    File contracts/ExecutionDelegate.sol, line 108:     require(revokedApproval[from] == false, "User has revoked approval");
    File contracts/ExecutionDelegate.sol, line 124:     require(revokedApproval[from] == false, "User has revoked approval");

# 7. [G-7] Arithmetics: uncheck blocks for arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible, some gas can be saved by using an `unchecked` block.

I suggest wrapping with an `unchecked` block here:

    File contracts/BlurExchange.sol, line 477:     uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
    File contracts/BlurExchange.sol, line 479:     totalFee += fee;
    File contracts/BlurExchange.sol, line 485:     uint256 receiveAmount = price - totalFee;
    File contracts/BlurExchange.sol, line 479:     totalFee += fee;
    File contracts/BlurExchange.sol, line 479:     totalFee += fee;

# 8. [G-8] Variables: Cache read variables in memory will save gas

File contracts/PolicyManager.sol, function viewWhitelistedPolicies, I suggest create a memory variable of the storage variable `_whitelistedPolicies`, and use it instead.

# 9. [G-9] Storage: Emitting storage values

The values emitted shouldn't be read from storage. The existing memory values should be used instead in here:

    File contracts/BlurExchange.sol, line 220-221:     
           executionDelegate = _executionDelegate;
           emit NewExecutionDelegate(executionDelegate); // I suggest:  emit NewExecutionDelegate(_executionDelegate);

    File contracts/BlurExchange.sol, line 229-230:     
           policyManager = _policyManager;
           emit NewPolicyManager(policyManager); // I suggest:  emit NewPolicyManager(_policyManager);

    File contracts/BlurExchange.sol, line 238-239:     
           oracle = _oracle;
           emit NewOracle(oracle); // I suggest:  emit NewOracle(_oracle);

    File contracts/BlurExchange.sol, line 246-247:     
           blockRange = _blockRange;
           emit NewBlockRange(blockRange); // I suggest:  emit NewBlockRange(_blockRange);


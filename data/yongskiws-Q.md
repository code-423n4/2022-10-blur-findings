
🪲Missing zero check 
Impact : Low

❌BlurExchange.initialize(uint256,address,IExecutionDelegate,IPolicyManager,address,uint256)._weth (contracts/BlurExchange.sol:97) lacks a zero-check on :
	• weth = _weth (contracts/BlurExchange.sol#113)

❌BlurExchange.initialize(uint256,address,IExecutionDelegate,IPolicyManager,address,uint256)._oracle (contracts/BlurExchange.sol:100) lacks a zero-check on :
	• oracle = _oracle (contracts/BlurExchange.sol#116)

Detect missing zero address validation.
Bob calls updateOwner without specifying the newOwner, so Bob loses ownership of the contract.

Recommendation
Check that the address is not zero.

🪲Conformity to Solidity naming conventions
Impact : Informational
Solidity defines a naming convention that should be followed.
https://docs.soliditylang.org/en/v0.4.25/style-guide.html#naming-conventions
❌ Function MerkleVerifier._computeRoot(bytes32,bytes32[]) (contracts/lib/MerkleVerifier.sol:33-43) is not in mixedCase
❌ Constant BlurExchange.version (contracts/BlurExchange.sol:58) is not in UPPER_CASE_WITH_UNDERSCORES
❌ Function MerkleVerifier._verifyProof(bytes32,bytes32,bytes32[]) (contracts/lib/MerkleVerifier.sol:17-26) is not in mixedCase
❌ Parameter BlurExchange.initialize(uint256,address,IExecutionDelegate,IPolicyManager,address,uint256)._blockRange (contracts/BlurExchange.sol:101) is not in mixedCase
❌ Parameter BlurExchange.initialize(uint256,address,IExecutionDelegate,IPolicyManager,address,uint256)._executionDelegate (contracts/BlurExchange.sol:98) is not in mixedCase
❌ Parameter BlurExchange.initialize(uint256,address,IExecutionDelegate,IPolicyManager,address,uint256)._oracle (contracts/BlurExchange.sol:100) is not in mixedCase
❌ Parameter BlurExchange.initialize(uint256,address,IExecutionDelegate,IPolicyManager,address,uint256)._policyManager (contracts/BlurExchange.sol:99) is not in mixedCase
❌ Parameter BlurExchange.initialize(uint256,address,IExecutionDelegate,IPolicyManager,address,uint256)._weth (contracts/BlurExchange.sol:97) is not in mixedCase
❌ Parameter BlurExchange.setBlockRange(uint256)._blockRange (contracts/BlurExchange.sol:242) is not in mixedCase
❌ Parameter BlurExchange.setExecutionDelegate(IExecutionDelegate)._executionDelegate (contracts/BlurExchange.sol:215) is not in mixedCase
❌ Parameter BlurExchange.setOracle(address)._oracle (contracts/BlurExchange.sol:233) is not in mixedCase
❌ Parameter BlurExchange.setPolicyManager(IPolicyManager)._policyManager (contracts/BlurExchange.sol:224) is not in mixedCase
❌ Parameter ExecutionDelegate.approveContract(address)._contract (contracts/ExecutionDelegate.sol:36) is not in mixedCase
❌ Parameter ExecutionDelegate.denyContract(address)._contract (contracts/ExecutionDelegate.sol:45) is not in mixedCase

🪲Dangerous usage of `block.timestamp` (timestamp)
Impact : low

❌ BlurExchange._canSettleOrder(uint256,uint256) (contracts/BlurExchange.sol:278-284) uses timestamp for comparisons
	• (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime) (contracts/BlurExchange.sol#283)

❌ BlurExchange._validateOrderParameters(Order,bytes32) (contracts/BlurExchange.sol:258-271) uses timestamp for comparisons
	• ((order.trader != address(0)) && (cancelledOrFilled[orderHash] == false) && _canSettleOrder(order.listingTime,order.expirationTime)) (contracts/BlurExchange.sol#263-270)

Recommendation
Avoid relying on `block.timestamp`.

🪲Assembly usage (assembly)
Impact : Informational

❌ MerkleVerifier._efficientHash(bytes32,bytes32) (contracts/lib/MerkleVerifier.sol:49-58) uses assembly
	• INLINE ASM (contracts/lib/MerkleVerifier.sol#53-57)

❌ BlurExchange._exists(address) (contracts/BlurExchange.sol:548-558) uses assembly
	• INLINE ASM (contracts/BlurExchange.sol#554-556)

Recommendation
Do not use `evm` assembly.

🪲boolean equal
Impact : Informational
Boolean constants can be used directly and do not need to be compare to true or false.

❌ BlurExchange._validateOrderParameters(Order,bytes32) (contracts/BlurExchange.sol:258-271) compares to a boolean constant:
	•((order.trader != address(0)) && (cancelledOrFilled[orderHash] == false) && _canSettleOrder(order.listingTime,order.expirationTime)) (contracts/BlurExchange.sol#263-270)

❌ ExecutionDelegate.transferERC1155(address,address,address,uint256,uint256) (contracts/ExecutionDelegate.sol:104-110) compares to a boolean constant:
	•require(bool,string)(revokedApproval[from] == false,User has revoked approval) (contracts/ExecutionDelegate.sol#108)

❌ ExecutionDelegate.transferERC20(address,address,address,uint256) (contracts/ExecutionDelegate.sol:119-126) compares to a boolean constant:
	•require(bool,string)(revokedApproval[from] == false,User has revoked approval) (contracts/ExecutionDelegate.sol#124)

❌ ExecutionDelegate.transferERC721(address,address,address,uint256) (contracts/ExecutionDelegate.sol:88-94) compares to a boolean constant:
	•require(bool,string)(revokedApproval[from] == false,User has revoked approval) (contracts/ExecutionDelegate.sol#92)

❌ ExecutionDelegate.transferERC721Unsafe(address,address,address,uint256) (contracts/ExecutionDelegate.sol:73-79) compares to a boolean constant:
	•require(bool,string)(revokedApproval[from] == false,User has revoked approval) (contracts/ExecutionDelegate.sol#77)

Recommendation
Remove the equality to the boolean constant.



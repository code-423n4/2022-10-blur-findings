## none-critical issue foundings
### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
BlurExchange.sol, 40, b'    event Opened();'
BlurExchange.sol, 41, b'    event Closed();'
BlurExchange.sol, 85, b'    event OrderCancelled(bytes32 hash);'
BlurExchange.sol, 86, b'    event NonceIncremented(address trader, uint256 newNonce);'
BlurExchange.sol, 88, b'    event NewExecutionDelegate(IExecutionDelegate executionDelegate);'
BlurExchange.sol, 89, b'    event NewPolicyManager(IPolicyManager policyManager);'
BlurExchange.sol, 90, b'    event NewOracle(address oracle);'
BlurExchange.sol, 91, b'    event NewBlockRange(uint256 blockRange);'

## low risks
### L01: the assert() function when false, uses up all the remaining gas and reverts all the changes made.
#### problem
assert(false) compiles to 0xfe, which is an invalid opcode, using up all remaining gas, and reverting all changes.
require(false) compiles to 0xfd which is the REVERT opcode, meaning it will refund the remaining gas. The opcode can also return a value (useful for debugging), but I don't believe that is supported in Solidity as of this moment. (2017-11-21)
#### prof
lib/ERC1967Proxy.sol, 22, b'        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));'

### L02: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
BlurExchange.sol, 113, b'        weth = _weth;'
BlurExchange.sol, 114, b'        executionDelegate = _executionDelegate;'
BlurExchange.sol, 115, b'        policyManager = _policyManager;'
BlurExchange.sol, 116, b'        oracle = _oracle;'
BlurExchange.sol, 220, b'        executionDelegate = _executionDelegate;'
BlurExchange.sol, 229, b'        policyManager = _policyManager;'
BlurExchange.sol, 238, b'        oracle = _oracle;'
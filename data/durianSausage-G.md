### G01: custom error save more gas
#### problem
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained https://blog.soliditylang.org/2021/04/21/custom-errors/. 
Custom errors are defined using the error statement.
#### prof
PolicyManager.sol, 26, b'        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");'
PolicyManager.sol, 37, b'        require(_whitelistedPolicies.contains(policy), "Not whitelisted");'
BlurExchange.sol, 139, b'        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");'
BlurExchange.sol, 140, b'        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");'
BlurExchange.sol, 142, b'        require(_validateSignatures(sell, sellHash), "Sell failed authorization");'
BlurExchange.sol, 143, b'        require(_validateSignatures(buy, buyHash), "Buy failed authorization");'
BlurExchange.sol, 219, b'        require(address(_executionDelegate) != address(0), "Address cannot be zero");'
BlurExchange.sol, 228, b'        require(address(_policyManager) != address(0), "Address cannot be zero");'
BlurExchange.sol, 237, b'        require(_oracle != address(0), "Address cannot be zero");'
BlurExchange.sol, 318, b'            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");'
BlurExchange.sol, 407, b'        require(v == 27 || v == 28, "Invalid v parameter");'
BlurExchange.sol, 428, b'            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");'
BlurExchange.sol, 424, b'            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");'
BlurExchange.sol, 431, b'        require(canMatch, "Orders cannot be matched");'
BlurExchange.sol, 482, b'        require(totalFee <= price, "Total amount of fees are more than the price");'
BlurExchange.sol, 534, b'        require(_exists(collection), "Collection does not exist");'
ExecutionDelegate.sol, 77, b'        require(revokedApproval[from] == false, "User has revoked approval");'
ExecutionDelegate.sol, 92, b'        require(revokedApproval[from] == false, "User has revoked approval");'
ExecutionDelegate.sol, 108, b'        require(revokedApproval[from] == false, "User has revoked approval");'
ExecutionDelegate.sol, 124, b'        require(revokedApproval[from] == false, "User has revoked approval");'

### G02: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706
#### prof
BlurExchange.sol, 557, b'        return size > 0;'

### G03: PREFIX INCREMENT SAVE MORE GAS
#### problem
prefix increment ++i is more cheaper than postfix i++
#### prof
PolicyManager.sol, 77, b'        for (uint256 i = 0; i < length; i++) {'
lib/MerkleVerifier.sol, 38, b'        for (uint256 i = 0; i < proof.length; i++) {'
lib/EIP712.sol, 77, b'        for (uint256 i = 0; i < fees.length; i++) {'
BlurExchange.sol, 199, b'        for (uint8 i = 0; i < orders.length; i++) {'
BlurExchange.sol, 476, b'        for (uint8 i = 0; i < fees.length; i++) {'

### G04: X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
#### prof
BlurExchange.sol, 208, b'        nonces[msg.sender] += 1;'

### G05: USING BOOLS FOR STORAGE INCURS OVERHEAD
#### problem
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
#### prof
BlurExchange.sol, 71, b'    mapping(bytes32 => bool) public cancelledOrFilled;'
ExecutionDelegate.sol, 18, b'    mapping(address => bool) public contracts;'
ExecutionDelegate.sol, 19, b'    mapping(address => bool) public revokedApproval;'

### G06: resign the default value to the variables.
#### problem
 resign the default value to the variables will cost more gas.
#### prof
PolicyManager.sol, 77, b'        for (uint256 i = 0; i < length; i++) {'
lib/MerkleVerifier.sol, 38, b'        for (uint256 i = 0; i < proof.length; i++) {'
lib/EIP712.sol, 77, b'        for (uint256 i = 0; i < fees.length; i++) {'
BlurExchange.sol, 199, b'        for (uint8 i = 0; i < orders.length; i++) {'
BlurExchange.sol, 475, b'        uint256 totalFee = 0;'
BlurExchange.sol, 476, b'        for (uint8 i = 0; i < fees.length; i++) {'

### G07: ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
#### problem
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
#### prof
PolicyManager.sol, 77, b'        for (uint256 i = 0; i < length; i++) {'
lib/MerkleVerifier.sol, 38, b'        for (uint256 i = 0; i < proof.length; i++) {'
lib/EIP712.sol, 77, b'        for (uint256 i = 0; i < fees.length; i++) {'
BlurExchange.sol, 199, b'        for (uint8 i = 0; i < orders.length; i++) {'
BlurExchange.sol, 476, b'        for (uint8 i = 0; i < fees.length; i++) {'

### G08：ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
#### prof
lib/EIP712.sol, 45, b'            abi.encode('
lib/EIP712.sol, 61, b'            abi.encode('
lib/EIP712.sol, 91, b'                abi.encode('
lib/EIP712.sol, 107, b'                abi.encode(nonce)'
lib/EIP712.sol, 132, b'            keccak256(abi.encode('
lib/EIP712.sol, 147, b'            keccak256(abi.encode('

### G09: Splitting require() statements that use && saves gas
#### problem
See this issue(https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
#### prof:
BlurExchange.sol, 407, b'        require(v == 27 || v == 28, "Invalid v parameter");'

### G10: FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
PolicyManager.sol, 30, b'    function addPolicy(address policy) external override onlyOwner '
PolicyManager.sol, 30, b'    function addPolicy(address policy) external override onlyOwner '
PolicyManager.sol, 41, b'    function removePolicy(address policy) external override onlyOwner '
PolicyManager.sol, 41, b'    function removePolicy(address policy) external override onlyOwner '
BlurExchange.sol, 46, b'    function open() external onlyOwner '
BlurExchange.sol, 46, b'    function open() external onlyOwner '
BlurExchange.sol, 50, b'    function close() external onlyOwner '
BlurExchange.sol, 50, b'    function close() external onlyOwner '
BlurExchange.sol, 53, b'    function _authorizeUpgrade(address) internal override onlyOwner '
BlurExchange.sol, 53, b'    function _authorizeUpgrade(address) internal override onlyOwner '
BlurExchange.sol, 222, b'    function setExecutionDelegate(IExecutionDelegate _executionDelegate)\n        external\n        onlyOwner\n    '
BlurExchange.sol, 231, b'    function setPolicyManager(IPolicyManager _policyManager)\n        external\n        onlyOwner\n    '
BlurExchange.sol, 240, b'    function setOracle(address _oracle)\n        external\n        onlyOwner\n    '
BlurExchange.sol, 248, b'    function setBlockRange(uint256 _blockRange)\n        external\n        onlyOwner\n    '
ExecutionDelegate.sol, 39, b'    function approveContract(address _contract) onlyOwner external '
ExecutionDelegate.sol, 39, b'    function approveContract(address _contract) onlyOwner external '
ExecutionDelegate.sol, 48, b'    function denyContract(address _contract) onlyOwner external '
ExecutionDelegate.sol, 48, b'    function denyContract(address _contract) onlyOwner external '

### G11: USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
#### problem:
We can save getter function of public constants.
#### prof:
lib/EIP712.sol, 22, b'    bytes32 constant public FEE_TYPEHASH = keccak256(\n        "Fee(uint16 rate,address recipient)"\n    );'
lib/EIP712.sol, 25, b'    bytes32 constant public ORDER_TYPEHASH = keccak256(\n        "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"\n    );'
lib/EIP712.sol, 28, b'    bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(\n        "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"\n    );'
lib/EIP712.sol, 31, b'    bytes32 constant public ROOT_TYPEHASH = keccak256(\n        "Root(bytes32 root)"\n    );'
BlurExchange.sol, 57, b'    string public constant name = "Blur Exchange";'
BlurExchange.sol, 58, b'    string public constant version = "1.0";'
BlurExchange.sol, 59, b'    uint256 public constant INVERSE_BASIS_POINT = 10000;'

### G12: USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
#### problem
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
#### prof
PolicyManager.sol, 27, b'        _whitelistedPolicies.add(policy);'
PolicyManager.sol, 37, b'        require(_whitelistedPolicies.contains(policy), "Not whitelisted");'
PolicyManager.sol, 38, b'        _whitelistedPolicies.remove(policy);'
PolicyManager.sol, 48, b'        return _whitelistedPolicies.contains(policy);'
PolicyManager.sol, 77, b'        for (uint256 i = 0; i < length; i++) {'
lib/MerkleVerifier.sol, 38, b'        for (uint256 i = 0; i < proof.length; i++) {'
lib/ERC1967Proxy.sol, 22, b'        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));'
lib/EIP712.sol, 47, b'                keccak256(bytes(eip712Domain.name)),'
lib/EIP712.sol, 48, b'                keccak256(bytes(eip712Domain.version)),'
lib/EIP712.sol, 63, b'                fee.rate,'
lib/EIP712.sol, 77, b'        for (uint256 i = 0; i < fees.length; i++) {'
lib/EIP712.sol, 90, b'            bytes.concat('
BlurExchange.sol, 199, b'        for (uint8 i = 0; i < orders.length; i++) {'
BlurExchange.sol, 199, b'        for (uint8 i = 0; i < orders.length; i++) {'
BlurExchange.sol, 199, b'        for (uint8 i = 0; i < orders.length; i++) {'
BlurExchange.sol, 199, b'        for (uint8 i = 0; i < orders.length; i++) {'
BlurExchange.sol, 219, b'        require(address(_executionDelegate) != address(0), "Address cannot be zero");'
BlurExchange.sol, 228, b'        require(address(_policyManager) != address(0), "Address cannot be zero");'
BlurExchange.sol, 237, b'        require(_oracle != address(0), "Address cannot be zero");'
BlurExchange.sol, 316, b'        if (order.order.expirationTime == 0) {'
BlurExchange.sol, 365, b'        return _recover(hashToSign, v, r, s) == trader;'
BlurExchange.sol, 365, b'        return _recover(hashToSign, v, r, s) == trader;'
BlurExchange.sol, 383, b'        uint8 v; bytes32 r; bytes32 s;'
BlurExchange.sol, 388, b'            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));'
BlurExchange.sol, 388, b'            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));'
BlurExchange.sol, 388, b'            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));'
BlurExchange.sol, 389, b'            v = _v; r = _r; s = _s;'
BlurExchange.sol, 392, b'        return _recover(oracleHash, v, r, s) == oracle;'
BlurExchange.sol, 392, b'        return _recover(oracleHash, v, r, s) == oracle;'
BlurExchange.sol, 407, b'        require(v == 27 || v == 28, "Invalid v parameter");'
BlurExchange.sol, 407, b'        require(v == 27 || v == 28, "Invalid v parameter");'
BlurExchange.sol, 407, b'        require(v == 27 || v == 28, "Invalid v parameter");'
BlurExchange.sol, 407, b'        require(v == 27 || v == 28, "Invalid v parameter");'
BlurExchange.sol, 408, b'        return ecrecover(digest, v, r, s);'
BlurExchange.sol, 408, b'        return ecrecover(digest, v, r, s);'
BlurExchange.sol, 451, b'        if (paymentToken == address(0)) {'
BlurExchange.sol, 475, b'        uint256 totalFee = 0;'
BlurExchange.sol, 476, b'        for (uint8 i = 0; i < fees.length; i++) {'
BlurExchange.sol, 476, b'        for (uint8 i = 0; i < fees.length; i++) {'
BlurExchange.sol, 476, b'        for (uint8 i = 0; i < fees.length; i++) {'
BlurExchange.sol, 476, b'        for (uint8 i = 0; i < fees.length; i++) {'
BlurExchange.sol, 502, b'        if (amount == 0) {'
BlurExchange.sol, 506, b'        if (paymentToken == address(0)) {'
BlurExchange.sol, 557, b'        return size > 0;'
Gas report (37 findings)

##1. A variable is being assigned its default value which is unnecessary.
Removing the assignment will save gas when deploying.
*There are 2 instances of this issue:*
```
bool reentrancyLock = false;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10
```
uint256 totalFee = 0;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475
##2. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves gas per loop.
*There are 5 instances of this issue:*
```
for (uint256 i = 0; i < length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77
```
for (uint256 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
```
for (uint256 i = 0; i < proof.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38
```
for (uint8 i = 0; i < orders.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
```
for (uint8 i = 0; i < fees.length; i++) 
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

##3. It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied
Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings
*There are 5 instances of this issue:*
```
for (uint256 i = 0; i < length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77
```
for (uint256 i = 0; i < fees.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
```
for (uint256 i = 0; i < proof.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

```
for (uint8 i = 0; i < orders.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
```
for (uint8 i = 0; i < fees.length; i++) 
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476
##4. require()/revert() strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas
```
require(contracts[msg.sender], "Contract is not approved to make transfers");
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22


##5. Use of constant keccak variables results in extra hashing (and so gas).
This results in the keccak operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas.
See: ethereum/solidity#9232 (comment)
*There are 5 instances of this issue:*
```
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
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L35



##6. Change  encode to encodePacked
In the contract EIP712, change abi.encode to abi.encodePacked can save gas.
*There are 3 instances of this issue:*
```
return keccak256(
            abi.encode(
                EIP712DOMAIN_TYPEHASH,
                keccak256(bytes(eip712Domain.name)),
                keccak256(bytes(eip712Domain.version)),
                eip712Domain.chainId,
                eip712Domain.verifyingContract
            )
        );

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L45
```
return keccak256(
            abi.encode(
                FEE_TYPEHASH,
                fee.rate,
                fee.recipient
            )
        );
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L61
```
return keccak256(
            bytes.concat(
                abi.encode(
                      ORDER_TYPEHASH,
                      order.trader,
                      order.side,
                      order.matchingPolicy,
                      order.collection,
                      order.tokenId,
                      order.amount,
                      order.paymentToken,
                      order.price,
                      order.listingTime,
                      order.expirationTime,
                      _packFees(order.fees),
                      order.salt,
                      keccak256(order.extraParams)
                ),
                abi.encode(nonce)
            )
        );

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L91-L109

##7. Redundant Code Statement 
From Pragma 0.8.0, ABI coder v2 is activated by default. The pragma abicoder v2 can be deleted from the repository. That will provide gas optimization.
*There are 2 instances of this issue:* 
```
pragma abicoder v2;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L3
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L3








##8. Switching between 1, 2 instead of 0, 1 is more gas efficient

The isOpen  variable is initially set to zero, set to a nonzero value and then reset to zero:
```
function open() external onlyOwner {
        isOpen = 1;
        emit Opened();
    }
    function close() external onlyOwner {
        isOpen = 0;
        emit Closed();
    }

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L43-L50
We then have to the higher cost for writing to clean storage rather than dirty storage (which is then refunded). This is not recommended as it can cause the size of the gas refunded to users to be capped. For more info see the OZ implementation:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/4a9cc8b4918ef3736229a5cc5a310bdc17bf759f/contracts/security/ReentrancyGuard.sol#L29-L35
Change from 0->1->0 to 1→2→1

##9. uint8 is less efficient than uint256 in loop iterations
The loop use uint8 for an index parameter (i). It does not give any efficiency, actually, it is the opposite as EVM operates on default of 256-bit values so uint8 is more expensive in this case as it needs a conversion. It only gives improvements in cases where you can pack variables together, e.g. structs.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Use a larger size then downcast where needed
*There are 2 instances of this issue:* 
```
for (uint8 i = 0; i < orders.length; i++) {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
```
for (uint8 i = 0; i < fees.length; i++) 
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

## 10. Add unchecked {} for subtractions where the operands cannot overflow/underflow
*There are 2 instances of this issue:* 
```
nonces[msg.sender] += 1;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208
```
require(totalFee <= price, "Total amount of fees are more than the price");
uint256 receiveAmount = price - totalFee;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L485

## 10. x += y consumes more gas than x = x + y for state variables
*There are 2 instances of this issue:* 
```
nonces[msg.sender] += 1;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208

```
 totalFee += fee;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L479



##11. Avoid use of state variables in event emissions to save gas
Where possible, use equivalent function parameters or local variables in event emits instead of state variables to prevent expensive SLOADs. Post-Berlin, SLOADs on state variables accessed first-time in a transaction increased from 800 gas to 2100, which is a 2.5x increase.
*There are 4 instances of this issue:* 
```
function setExecutionDelegate(IExecutionDelegate _executionDelegate)
        external
        onlyOwner
    {
        require(address(_executionDelegate) != address(0), "Address cannot be zero");
        executionDelegate = _executionDelegate;
        emit NewExecutionDelegate(executionDelegate);
    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L221
```
function setPolicyManager(IPolicyManager _policyManager)
        external
        onlyOwner
    {
        require(address(_policyManager) != address(0), "Address cannot be zero");
        policyManager = _policyManager;
        emit NewPolicyManager(policyManager);
    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L230
```
function setOracle(address _oracle)
        external
        onlyOwner
    {
        require(_oracle != address(0), "Address cannot be zero");
        oracle = _oracle;
        emit NewOracle(oracle);
    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L239
```
function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {
        blockRange = _blockRange;
        emit NewBlockRange(blockRange);
    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L247
##12. > 0 can be replaced with != 0 for gas optimisation
```
return size > 0;

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L557






##13. Using bools for storage incurs overhead ????????????????
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false 
```
function approveContract(address _contract) onlyOwner external {
        contracts[_contract] = true;
        emit ApproveContract(_contract);
    }

    /**
     * @dev Revoke approval of contract to call transfer functions
     * @param _contract address of contract to revoke approval
     */
    function denyContract(address _contract) onlyOwner external {
        contracts[_contract] = false;
        emit DenyContract(_contract);
    }

    /**
     * @dev Block contract from making transfers on-behalf of a specific user
     */
    function revokeApproval() external {
        revokedApproval[msg.sender] = true;
        emit RevokeApproval(msg.sender);
    }

    /**
     * @dev Allow contract to make transfers on-behalf of a specific user
     */
    function grantApproval() external {
        revokedApproval[msg.sender] = false;
        emit GrantApproval(msg.sender);
    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L18-L24
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L36-L64
##14. Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Saves  gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table
```
/* Constants */
    string public constant name = "Blur Exchange";
    string public constant version = "1.0";
    uint256 public constant INVERSE_BASIS_POINT = 10000;
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57-L59

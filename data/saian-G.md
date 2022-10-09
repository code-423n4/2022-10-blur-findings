
# Switching from non-zero to non-zero is more efficient

By switching from non-zero to non-zero value, expensive SSTORE from 0/false to non-zero can be avoided. 
and when boolean is used, writing to storage costs more than uint256 as each write operation has to perform extra read operation to correctly place the boolean value in the slot.
So switching from 0/false to 1/true can be replaced by 1 and 2

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/86bd4d73896afcb35a205456e361436701823c7a/contracts/security/ReentrancyGuard.sol#L29-L33

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L13

```
    modifier reentrancyGuard {
        require(!reentrancyLock, "Reentrancy detected");
        reentrancyLock = true;
        _;
        reentrancyLock = false;
    }
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L43

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

# Reduce size of revert string 

Revert strings that are longer than 32 bytes requires at least one additional mstore, along with additional overhead for computing memory offset, etc.
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
	
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22

```
    require(contracts[msg.sender], "Contract is not approved to make transfers");
```

# Comparison with bool literal

Comparison to boolean literal is more expensive than directly checking the value.
Comparison with `false` can be replaced by using the `!` operator with the value

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77

```
    require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92

```       
    require(revokedApproval[from] == false, "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108

```
    require(revokedApproval[from] == false, "User has revoked approval");   
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124

```
    require(revokedApproval[from] == false, "User has revoked approval");   
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L267

```
    (cancelledOrFilled[orderHash] == false)
```
# constants can be internal/private

Marking constants as private/internal saves gas on deployment as compiler does not have to create a getter function for these variables, these variables can still be read from the verfied source code or the bytecode.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L33

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

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57

```    
    string public constant name = "Blur Exchange";  
    string public constant version = "1.0";

```
# Cache array length

Array length can be cached in a variable and used in the loop instead of read from storage in every iteration

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

```       
    for (uint256 i = 0; i < fees.length; i++)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

```
    for (uint256 i = 0; i < proof.length; i++)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

```
    for (uint8 i = 0; i < orders.length; i++
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

```
    for (uint8 i = 0; i < fees.length; i++) 
```

# Function used once can be inlined

Functions used once can be inlined to save gas on performing JUMP between the functions.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L548

```
    function _exists(address what)
        internal
        view
        returns (bool)
    {
        uint size;
        assembly {
            size := extcodesize(what)
        }
        return size > 0;
    }
```

# bytes32 instead of string

If data can fit into 32 bytes, then using bytes32 instead of string is cheaper, as string is a dynamically sized data structures and therefore consumes more gas.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57

```    
    string public constant name = "Blur Exchange";  
    string public constant version = "1.0";
```
# Custom errors

Custom error from solidity **0.8.4** are cheaper than revert strings with cheaper deployment cost and runtime cost when the revert condition is met. 

https://blog.soliditylang.org/2021/04/21/custom-errors/

# Use `unchecked` to save gas

If an expression cannnot overflow/underflow it can be placed in a unchecked block to avoid the implicit overflow/underflow checks introduced in 0.8.0 and save gas 

https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L485

```
    require(totalFee <= price, "Total amount of fees are more than the price");

    /* Amount that will be received by seller. */
    uint256 receiveAmount = price - totalFee; 
```

# Variables less than 32 bytes

Due to how the EVM natively works on 256 bit numbers, using a 8 bit number in for-loops introduces additional costs as the EVM has to properly enforce the limits of this smaller type.

https://docs.soliditylang.org/en/v0.8.0/internals/layout_in_storage.html#layout-of-state-variables-in-storage

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

```
    for (uint8 i = 0; i < orders.length; i++)
```

# Avoid initialization with default values

When variables are not set, it is assumed to have it's default value(0 for uint, false for bool, address(0) for address). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10

```
    bool reentrancyLock = false;
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475

```
        uint256 totalFee = 0; 
```

# Use `++x` instead of `x += 1`

Pre-increment `++x` costs less when compared to `x += 1` or `x++` for unsigned integers

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208

```
    nonces[msg.sender] += 1;
```

# Use function arguments instead of storage variables in event emits

In event emits using local variables or function arguments instead of storage variable can avoid storage read and save gas

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L221

```
        emit NewExecutionDelegate(executionDelegate);   
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L230

```
        emit NewPolicyManager(policyManager); 
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L239

```
        emit NewOracle(oracle); 
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L247

```
        emit NewBlockRange(blockRange); 
```

# Unnecessary variables

Variables `_v`, `_r` and `_s` are unnecessary and the values can be directly assigned to the variabesl `v`, `r` and `s`

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L388

```
    (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
    v = _v; r = _r; s = _s; 

```
can be replaced with

```
    (bytes32[] memory merklePath, v, r, s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```
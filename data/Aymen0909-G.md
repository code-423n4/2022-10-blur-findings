# Gas Optimizations

## Findings

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | State variables that cannot be changed should be declared `immutable` in a constructor | 1 |
| 2      | Variables inside struct should be packed to save gas  | 1    |
| 3      | Using `bool` for storage incurs overhead   |  1 |
| 4      | Use `unchecked` blocks to save gas  |  1 |
| 5      | Use custom errors rather than `require()`/`revert()` strings to save deployments gas  |  24 |
| 6      | `array.length` should not be looked up in every loop in a for loop |  4 |
| 7      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  7  |
| 8      | Use of `++i` cost less gas than `i++` in for loops    |  5  |
| 9      | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  5  |
| 10      | Using `private` rather than `public` for constants, saves gas |  3 |
| 11      | Functions guaranteed to revert when called by normal users can be marked `payable` |  10 |

## Findings

### 1- State variables that cannot be changed should be declared `immutable` in a constructor :

If a state variable is set only once and then there is no way to change its value, it's better to set it directly in the constructor and declare it `immutable` for saving gas.

There is 1 instance of this issue:

File: contracts/BlurExchange.sol [Line 113](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L113)
```
weth = _weth;
```

Since there is no way of changing the `weth` address after setting it the first time, it's better to declare it as immutable (of course you'll need to add a constructor).

### 2- Variables inside `struct` should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

There is 1 instance of this issue:

File: contracts/lib/OrderStructs.sol [Line 13-28](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol#L13-L28)

```
struct Order {
    address trader;
    Side side;
    address matchingPolicy;
    address collection;
    uint256 tokenId;
    uint256 amount;
    address paymentToken;
    uint256 price;
    uint256 listingTime;
    /* Order expiration timestamp - 0 for oracle cancellations. */
    uint256 expirationTime;
    Fee[] fees;
    uint256 salt;
    bytes extraParams;
}
```

As `listingTime` and `expirationTime` can't really overflow 2^64, their values should be packed to save gas, the struct should be modified as follow  :

```
struct Order {
    address trader;
    Side side;
    address matchingPolicy;
    address collection;
    uint256 tokenId;
    uint256 amount;
    address paymentToken;
    uint256 price;
    uint64 listingTime;
    /* Order expiration timestamp - 0 for oracle cancellations. */
    uint64 expirationTime;
    Fee[] fees;
    uint256 salt;
    bytes extraParams;
}
```

### 3- Using `bool` for storage incurs overhead  :

The `contracts/lib/ReentrancyGuarded.sol` uses boolean for managing reentrancyLock which is not recommended as it costs a lot of gas, you should use `uint256(1)` and `uint256(2)` instead of `true/false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past. This method is explained and implemented in the [openzeppelin ReentrancyGuard](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L35) contract.

There is 1 instances of this issue:

File: contracts/lib/ReentrancyGuarded.sol [Line 10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)
```
10       bool reentrancyLock = false;
```

### 4- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There is 1 instance of this issue:

File: contracts/BlurExchange.sol [Line 485](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L485)

```
uint256 receiveAmount = price - totalFee;
```

The above operation cannot underflow due to the check [ require(totalFee <= price, "Total amount of fees are more than the price");](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482) and should be modified as follows :

```
uint256 receiveAmount;
unchecked {
    receiveAmount = price - totalFee;
}
```

### 5- Use custom errors rather than `require()`/`revert()` strings to save deployments gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are 24 instances of this issue :

```
File: contracts/BlurExchange.sol

36       require(isOpen == 1, "Closed");
139      require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
140      require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");    
142      require(_validateSignatures(sell, sellHash), "Sell failed authorization");
143      require(_validateSignatures(buy, buyHash), "Buy failed authorization");
219      require(address(_executionDelegate) != address(0), "Address cannot be zero");
228      require(address(_policyManager) != address(0), "Address cannot be zero");
237      require(_oracle != address(0), "Address cannot be zero");
318      require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
407      require(v == 27 || v == 28, "Invalid v parameter");
424      require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
428      require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
431      require(canMatch, "Orders cannot be matched");
482      require(totalFee <= price, "Total amount of fees are more than the price");
513      revert("Invalid payment token");
534      require(_exists(collection), "Collection does not exist");

File: contracts/ExecutionDelegate.sol

22       require(contracts[msg.sender], "Contract is not approved to make transfers");
77       require(revokedApproval[from] == false, "User has revoked approval");
92       require(revokedApproval[from] == false, "User has revoked approval");
108      require(revokedApproval[from] == false, "User has revoked approval");
124      require(revokedApproval[from] == false, "User has revoked approval");

File: contracts/PolicyManager.sol

26       require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
37       require(_whitelistedPolicies.contains(policy), "Not whitelisted");

File: contracts/lib/ReentrancyGuarded.sol

14       require(!reentrancyLock, "Reentrancy detected");
```

### 6- `array.length` should not be looked up in every loop in a for loop :

There are 4 instances of this issue:

File: contracts/BlurExchange.sol [Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
```
for (uint8 i = 0; i < orders.length; i++)
``` 

File: contracts/BlurExchange.sol [Line 476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
```
for (uint8 i = 0; i < fees.length; i++)
``` 

File: contracts/lib/EIP712.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
```
for (uint256 i = 0; i < fees.length; i++) 
``` 

File: contracts/lib/MerkleVerifier.sol [Line 38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
```
for (uint256 i = 0; i < proof.length; i++)
```

### 7- It costs more gas to initialize variables to zero than to let the default of zero be applied (saves ~3 gas per instance) :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 7 instances of this issue:

File: contracts/BlurExchange.sol [Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
```
for (uint8 i = 0; i < orders.length; i++)
``` 

File: contracts/BlurExchange.sol [Line 475](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475)
```
uint256 totalFee = 0;
``` 

File: contracts/BlurExchange.sol [Line 476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
```
for (uint8 i = 0; i < fees.length; i++)
``` 

File: contracts/PolicyManager.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
```
for (uint256 i = 0; i < length; i++)
``` 

File: contracts/lib/EIP712.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
```
for (uint256 i = 0; i < fees.length; i++) 
``` 

File: contracts/lib/MerkleVerifier.sol [Line 38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
```
for (uint256 i = 0; i < proof.length; i++)
```   

File: contracts/lib/ReentrancyGuarded.sol [Line 10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)
```
10       bool reentrancyLock = false;
```

### 8- Use of ++i cost less gas than i++ in for loops :

There are 5 instances of this issue:

File: contracts/BlurExchange.sol [Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
```
for (uint8 i = 0; i < orders.length; i++)
``` 

File: contracts/BlurExchange.sol [Line 476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
```
for (uint8 i = 0; i < fees.length; i++)
``` 

File: contracts/PolicyManager.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
```
for (uint256 i = 0; i < length; i++)
``` 

File: contracts/lib/EIP712.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
```
for (uint256 i = 0; i < fees.length; i++) 
``` 

File: contracts/lib/MerkleVerifier.sol [Line 38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
```
for (uint256 i = 0; i < proof.length; i++)
``` 

### 9- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 5 instances of this issue:

File: contracts/BlurExchange.sol [Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
```
for (uint8 i = 0; i < orders.length; i++)
``` 

File: contracts/BlurExchange.sol [Line 476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
```
for (uint8 i = 0; i < fees.length; i++)
``` 

File: contracts/PolicyManager.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
```
for (uint256 i = 0; i < length; i++)
``` 

File: contracts/lib/EIP712.sol [Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)
```
for (uint256 i = 0; i < fees.length; i++) 
``` 

File: contracts/lib/MerkleVerifier.sol [Line 38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
```
for (uint256 i = 0; i < proof.length; i++)
``` 

### 10- Using `private` rather than `public` for constants, saves gas :

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There are 3 instances of this issue:

File: contracts/BlurExchange.sol [Line 57](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57)

```
string public constant name = "Blur Exchange";
```

File: contracts/BlurExchange.sol [Line 58](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58)
```
string public constant version = "1.0";
``` 

File: contracts/BlurExchange.sol [Line 59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)
```
uint256 public constant INVERSE_BASIS_POINT = 10000;
``` 

### 11- Functions guaranteed to revert when called by normal users can be marked `payable` :

If a function modifier such as `onlyAdmin` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 10 instances of this issue:

```
File: contracts/BlurExchange.sol

43       function open() external onlyOwner
47       function close() external onlyOwner
215      function setExecutionDelegate(IExecutionDelegate _executionDelegate) external onlyOwner
224      function setPolicyManager(IPolicyManager _policyManager) external onlyOwner
233      function setOracle(address _oracle) external onlyOwner
242      function setBlockRange(uint256 _blockRange) external onlyOwner

File: contracts/ExecutionDelegate.sol

36       function approveContract(address _contract) onlyOwner external
45       function denyContract(address _contract) onlyOwner external

File: contracts/PolicyManager.sol

25       function addPolicy(address policy) external override onlyOwner
36       function removePolicy(address policy) external override onlyOwner
```
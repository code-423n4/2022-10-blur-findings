# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      | Front-runable `initialize` function | Low | 1 |
| 2      | Return values of ERC20 `transferFrom()` function not checked | Low | 1 |
| 3      | Setters should check the input value and revert if it's the zero address or zero | Low | 1 |
| 4      | `require()`/`revert()` statements should have descriptive reason strings | NC | 3 |
| 5      | Non-assembly method available | NC |  1 |
| 6      | Duplicated `require()` checks should be refactored to a modifier  | NC |  4 |
| 7      | Named return variables not used anywhere in the functions | NC | 1 |
| 8      | `constant` should be defined rather than using magic numbers  | NC | 4 |

## Findings

### 1- Front-runable `initialize` function :

The `initialize` function inside the BlurExchange contract is used to initialize the contract owner and important contract parameters (executionDelegate, policyManager, oracle), but any attacker can initialize the contract before the legitimate deployer and even if the developers when deploying call immediately the `initialize` function, malicious agents can trace the protocol deployment transactions and insert their own transaction between them and by doing so they front run the developers call and gain the ownership of the contract and set the wrong parameters.

The impact of this issue is : 

* In the best case developers notice the problem and have to redeploy the contract and thus costing more gas.

* In the worst case the protocol continue to work with the wrong owner and state parameters which could lead to the loss of users funds.

#### Risk : Low 

#### Proof of Concept

Issue occurs in the initialize function below :

File: contracts/BlurExchange.sol [Line 95-118](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L118)

```
function initialize(
    uint chainId,
    address _weth,
    IExecutionDelegate _executionDelegate,
    IPolicyManager _policyManager,
    address _oracle,
    uint _blockRange
) public initializer {
    __Ownable_init();
    isOpen = 1;

    DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
        name              : name,
        version           : version,
        chainId           : chainId,
        verifyingContract : address(this)
    }));

    weth = _weth;
    executionDelegate = _executionDelegate;
    policyManager = _policyManager;
    oracle = _oracle;
    blockRange = _blockRange;
}
```

#### Mitigation

It's recommended to use the constructor to initialize non-proxied contracts.

For initializing proxy (upgradable) contracts deploy them using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify that the transaction succeeded.

### 2- Return values of ERC20 `transferFrom()` function not checked :

Not all IERC20 implementations revert() when there’s a failure in `transfer()/`transferFrom()` which is the case for the WETH token, instead the function signature has a boolean return value that indicate errors when they occur. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment.

#### Risk : Low

#### Proof of Concept

Instances include:

File: contracts/BlurExchange.sol [Line 511](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L511)

```
executionDelegate.transferERC20(weth, from, to, amount);
```

The `_transferTo` function calls `executionDelegate.transferERC20` to make a weth token transfer, and the `executionDelegate.transferERC20` function return the boolean value got from calling `transferFrom()` which indicate if the transfer happend without a problem. This return value must be checked to avoid any silent failed transfer.

#### Mitigation

The `_transferTo` function should be modifier as follow to check the `transferFrom()` return value :

```
function _transferTo(
    address paymentToken,
    address from,
    address to,
    uint256 amount
) internal {
    if (amount == 0) {
        return;
    }

    if (paymentToken == address(0)) {
        /* Transfer funds in ETH. */
        payable(to).transfer(amount);
    } else if (paymentToken == weth) {
        /* Transfer funds in WETH. */

        /* 
            @audit add the check for the transferFrom return value and revert if it's false
         */
        require(executionDelegate.transferERC20(weth, from, to, amount), "Transfer_Failed");
    } else {
        revert("Invalid payment token");
    }
}
```

### 3- Setters should check the input value and revert if it's the zero address or zero  :

When setting a new value to a state variable the setter function must check the new value and revert if it's the zero address or zero.

#### Risk : Low

#### Proof of Concept

Instances include:

File: contracts/BlurExchange.sol

[function setBlockRange(uint256 _blockRange)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242-L248)

#### Mitigation
Add non-zero address/uint checks in the setters for the instances aforementioned.

### 4- `require()`/`revert()` statements should have descriptive reason strings :

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: File: contracts/BlurExchange.sol

[require(sell.order.side == Side.Sell);](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134)

[require(msg.sender == order.trader);](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183)

[require(msg.value == price);](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452)

#### Mitigation
Add reason strings to the aforementioned require statements for better comprehension.

### 5- Non-assembly method available :

`<address>.code.length` can be used in Solidity `>= 0.8.0` to access an account’s code size and check if it is a contract without inline assembly.

#### Risk : NON CRITICAL

#### Proof of Concept

There is 1 instance of this issue:

File: contracts/BlurExchange.sol [Line 548-564]()

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

#### Mitigation
Assembly method can be replaced it with : 

```
function _exists(address what)
    internal
    view
    returns (bool)
{
    return what.code.length > 0;
}
```

### 6- Duplicated `require()` checks should be refactored to a modifier :

#### Risk : NON CRITICAL

#### Proof of Concept

There are 4 instances of this issue:

```
File: contracts/ExecutionDelegate.sol

77       require(revokedApproval[from] == false, "User has revoked approval");
92       require(revokedApproval[from] == false, "User has revoked approval");
108      require(revokedApproval[from] == false, "User has revoked approval");
124      require(revokedApproval[from] == false, "User has revoked approval");
```

#### Mitigation
Those checks should be replaced by a `isApprovalRevoked` modifier as follow :

```
modifier isApprovalRevoked(address _from){
    require(!revokedApproval[_from], "User has revoked approval");
    _;
}
```

### 7- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/BlurExchange.sol

[returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L419)

#### Mitigation

Either use the named return variables inplace of the return statement or remove them.

### 8- `constant` should be defined rather than using magic numbers :

It is best practice to use constant variables rather than hex/numeric literal values to make the code easier to understand and maintain, but if they are used those numbers should be well docummented. 

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/lib/EIP712.sol

["\x19\x01"](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L118)

["\x19\x01"](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L130)

["\x19\x01"](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L145)

File: contracts/lib/ERC1967Proxy.sol

[keccak256("eip1967.proxy.implementation")](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L22)

#### Mitigation
Replace the hex/numeric literals aforementioned with `constants`.
## require statement does not have reason of failure

User will not know which steps goes wrong when transaction reverted.


https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134


```
        require(sell.order.side == Side.Sell);
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L183


```
        require(msg.sender == order.trader);
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452


```
            require(msg.value == price);
```

## Ownership can be transferred to address.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L13

the Policy Manager ownership can be transferred to 0, then no one can set matching policy.

## Address(0) policy can be added

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L25

address(0) can be added as policy

## EnumerableSet.AddressSet add and remove return value not handled in PolicyManager.sol

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L27

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L38

According to the implementation of 

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol,

the function

```solidity
function add(AddressSet storage set, address value) internal returns (bool) {
```

and 

```solidity
function remove(AddressSet storage set, address value) internal returns (bool) {
```

returns value, but when adding and removing policy, the return value are not properly handled.

## Openzepplein package is outdated.

the package used is 

```solidity
    "@openzeppelin/contracts": "4.4.1",
    "@openzeppelin/contracts-upgradeable": "^4.6.0",
```

we recommend the project upgrade to the newest implementation of the openzepplin to avoid any known vulnerability in the openzepplin  package

https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts
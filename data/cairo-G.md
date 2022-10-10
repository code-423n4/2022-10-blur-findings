<h3>Gas optimization #1</h3>

Replacing uint256 with bool in isOpen variable because it is a binary state (1 == True, 0 == False). 

Defining [variable](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L33):
```
/* Auth */
bool public isOpen;
```

Adjusting modifier [whenOpen](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L35):
```
modifier whenOpen() {
     require(isOpen, "Closed");
     _;
}
```

Adjusting [open](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L43) and [close](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L47) functions:
```
function open() external onlyOwner {
     isOpen = true;
     emit Opened();
}
```
```
function close() external onlyOwner {
     isOpen = false;
     emit Closed();
}
```

Adjusting variable in [initialize](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L104) function:
```
isOpen = true;
```

<h4>Gas results:</h4>
Methods:

- Increase gas cost for close method by 4,809 gas.
- Decreases gas cost for execute method by 1,766 gas.
- Decreases gas cost for open method by 17,082 gas.

Deployments:
- Decreases gas cost for ERC1967Proxy deployment by 21,862 gas.
- Increases gas cost for TestBlurExchange deployment by 16,788 gas.

**Net gas savings**: 19,113 gas

<h3>Gas optimization #2</h3>

Modifying [incrementNonce](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L207) with unchecked and ++ sum.

```
function incrementNonce() external {
     unchecked {
          nonces[msg.sender]++;
     }
     emit NonceIncremented(msg.sender, nonces[msg.sender]);
}
```
<h4>Gas results:</h4>
Methods:

- Decreases gas cost for incrementNonce method by 248 gas.

**Net gas savings**: 248 gas
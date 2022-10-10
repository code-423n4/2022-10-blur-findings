## **1.  `require()` should be used instead of `assert()`**

Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require)
 states that "The assert function creates an error of type 
Panic(uint256). ... Properly functioning code should never create a 
Panic, not even on invalid external input. If this happens, then there 
is a bug in your contract which you should fix".

## **Instances -**

### **File - ERC1967Proxy.sol**

22: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));

[https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L22)

## **2 . Lines are too long**

Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width)

 characters. Today’s screens are much larger so it’s reasonable to 
stretch this in some cases. Since the files will most likely reside in 
GitHub, and GitHub starts using a scroll bar in all cases when the 
length is over [164](https://github.com/aizatto/character-length)
 characters, the lines below should be split when they reach that length

## **Instances -**

### File:BlurExchange.sol

124:      * @dev Match two orders, ensuring validity of the match, and execute all associated state transitions. Protected against reentrancy by a contract-global lock.
125:      * @param sell Sell input
126:      * @param buy Buy input
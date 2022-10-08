## Unnecessary equals boolean


Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

### Code instances:

        BlurExchange.sol, 267: (cancelledOrFilled[orderHash] == false) &&
        ExecutionDelegate.sol, 124: require(revokedApproval[from] == false, "User has revoked approval");
        ExecutionDelegate.sol, 77: require(revokedApproval[from] == false, "User has revoked approval");
        ExecutionDelegate.sol, 92: require(revokedApproval[from] == false, "User has revoked approval");
        ExecutionDelegate.sol, 108: require(revokedApproval[from] == false, "User has revoked approval");



## State variables that could be set immutable

In the following files there are state variables that could be set immutable to save gas. 

### Code instance:

        weth in BlurExchange.sol



## Caching array length can save gas


Caching the array length is more gas efficient.
This is because access to a local variable in solidity is more efficient than query storage / calldata / memory.
We recommend to change from:    

    for (uint256 i=0; i<array.length; i++) { ... }

to: 

    uint len = array.length  
    for (uint256 i=0; i<len; i++) { ... }


### Code instances:

        BlurExchange.sol, fees, 476
        BlurExchange.sol, orders, 199
        MerkleVerifier.sol, proof, 38
        EIP712.sol, fees, 77



## Prefix increments are cheaper than postfix increments

Prefix increments are cheaper than postfix increments. 
Further more, using unchecked {++x} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change
There is no risk of overflow caused by increamenting the iteration index in for loops (the `++i` in `for (uint256 i = 0; i < numIterations; ++i)`).
But increments perform overflow checks that are not necessary in this case.
These functions use not using prefix increments (`++x`) or not using the unchecked keyword: 

### Code instances:

        change to prefix increment and unchecked: BlurExchange.sol, i, 476
        change to prefix increment and unchecked: BlurExchange.sol, i, 199
        change to prefix increment and unchecked: MerkleVerifier.sol, i, 38
        change to prefix increment and unchecked: PolicyManager.sol, i, 77
        change to prefix increment and unchecked: EIP712.sol, i, 77



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instances:

        MerkleVerifier.sol, 38
        PolicyManager.sol, 77
        BlurExchange.sol, 199
        BlurExchange.sol, 476
        EIP712.sol, 77



## Unnecessary default assignment


Unnecessary default assignments, you can just declare and it will save gas and have the same meaning.
    

### Code instance:

        ReentrancyGuarded.sol (L#10) : bool reentrancyLock = false; 



## Use bytes32 instead of string to save gas whenever possible


    Use bytes32 instead of string to save gas whenever possible.
    String is a dynamic data structure and therefore is more gas consuming then bytes32.

    
### Code instances:

        BlurExchange.sol (L58), string public constant version = "1.0";
        BlurExchange.sol (L57), string public constant name = "Blur Exchange";



## uint8 index

Due to how the EVM natively works on 256 numbers, using a 8 bit number here introduces additional costs as the EVM has to properly enforce the limits of this smaller type. 
See the warning at this link: https://docs.soliditylang.org/en/v0.8.0/internals/layout_in_storage.html#layout-of-state-variables-in-storage 
We recommend to use uint256 for the index in every for loop instead using uint8: 

### Code instances:

        BlurExchange.sol, uint8 i, 476
        BlurExchange.sol, uint8 i, 199



## Consider inline the following functions to save gas


    You can inline the following functions instead of writing a specific function to save gas.
    (see https://github.com/code-423n4/2021-11-nested-findings/issues/167 for a similar issue.)

    
### Code instances

        ERC1967Proxy.sol, _implementation, { return ERC1967Upgrade._getImplementation(); }
        MerkleVerifier.sol, _hashPair, { return a < b ? _efficientHash(a, b) : _efficientHash(b, a); }
        BlurExchange.sol, _canSettleOrder, { return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime); }
        EIP712.sol, _hashToSign, { return keccak256(abi.encodePacked( "\x19\x01", DOMAIN_SEPARATOR, orderHash )); }



## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        BlurExchange.sol, _executeTokenTransfer
        EIP712.sol, _hashFee
        BlurExchange.sol, _validateUserAuthorization
        BlurExchange.sol, _transferFees
        BlurExchange.sol, _canMatchOrders
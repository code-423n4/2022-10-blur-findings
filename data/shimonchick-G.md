# Gas optimizations
1. Structs inside OrderStructs.sol can be packed better to use less storage space. In solidity enums are converted to ```uint8``` and  multiple uint8 can be packed inside the same word. As a rule of thumb place variables that use less space first and next to each other and variable-sized types last. A better structuring of the Input struct would be:
```
struct Input {
    uint8 v;
    SignatureVersion signatureVersion; //uint8, this and the v value will get optimized to share the same word
    bytes32 r;
    bytes32 s;
    uint256 blockNumber;
    Order order;
    bytes extraSignature;
}
```


2. ERC1967Proxy.sol #22
Constructor does not need assert, since _IMPLEMENTATION_SLOT is a constant and ```keccak256("eip1967.proxy.implementation")``` is guaranteed to always return ```0x4910fdfa16fed3260ed0e7147f7cc6da11a60208b5b9406d12a635614ffd9143```.
- Alternatively, to save gas you can use an if statement + custom revert object.
- The cast to uint256 and then to bytes32 is pointless since the keccak256 method returns a bytes32 in the first place.
```if(_IMPLEMENTATION_SLOT != keccak256("eip1967.proxy.implementation") - 1) revert(INVALID_IMPLEMENTATION_HASH());```

3.BlurExchange.sol
- All requires can be substituted for if + custom error message
-  i++ can be substituted with unchecked{++i} inside the for loop
```      
       for (uint8 i = 0; i < orders.length; ) {
            cancelOrder(orders[i]);
            unchecked{
                ++i
            }
        }
```
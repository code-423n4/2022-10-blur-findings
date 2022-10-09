### USING UNCHECKED ARIMETHIC TO SAVE GAS FOR OPERATIONS THAT WILL NOT OVERFLOW

Use unchecked for arithmetic where you are sure it won't over or underflow, saving gas costs for checks added from solidity v0.8.0.

Instances Found:
- [BlurExchange.sol#L199-L201](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199-L201)
- [BlurExchange.sol#L476-L479](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476-L479)
- [PolicyManager.sol#L77-L79](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77-L79)

Example Implementation:

Instead of ```    for (uint8 i = 0; i < orders.length; i++) {
            cancelOrder(orders[i]);
        }``` use ```  for (uint8 i = 0; i < orders.length; ) {
            cancelOrder(orders[i]);
            unchecked{ i++};
        }```

### USE A VARIABLE TO CACHE ARRAY LENGTH AND READ FROM IT,  <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Even memory arrays incur the overhead of bit tests and bit shifts to calculate the array length. Storage array length checks incur an extra Gwarmaccess (100 gas) PER-LOOP.

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Instance Found:
- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)

Mitigation: 
Use a variable i.e. ```uint256 length = order.length;``` to cache it and then use the variable to be read in the for-loop i.e. ```for (uint8 i = 0; i < length; i++) {
            cancelOrder(orders[i]);
        }```

### INCREMENTING A STORAGE VALUE WHILE EMITTING THE INCREMENT’S RESULT SAVES GAS RATHER THAN INCREMENTING FIRST THEN EMITTING

Instances Found:

- [BlurExchange.sol#L209](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L209)

This can be optimised from ``` nonces[msg.sender] += 1;
        emit NonceIncremented(msg.sender, nonces[msg.sender]);``` to ``` emit NonceIncremented(msg.sender, ++nonces[msg.sender]);```


### ++i COSTS 5 GAS LESS THAN i++ WHICH COST 6 GAS LESS THAN i += 1

Instances Found:

- [BlurExchange.sol#L208](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L208)

This can be optimised from ``` nonces[msg.sender] += 1;``` to ``` ++nonces[msg.sender];```

### USING BOOLS FOR STORAGE INCURS OVERHEAD, USE UINT256 INSTEAD FOR BOOLEAN MAPPINGS

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's content, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Use ``uint256(0)`` as default FALSE and ``uint256(1)`` as TRUE flag instead of ``bool`` true/false to avoid a Gwarmaccess (100 Gas) for the extra SLOAD, and to avoid Gsset (20000 Gas) when changing from 'false' to 'true', after having 'true' in the past.

Instances Found:
- [BlurExchange.sol#L71](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L71)
- [ExecutionDelegate.sol#L18-L19](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L18-L19)


### USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

Require statements like ``` require(!_whitelistedPolicies.contains(policy), "Already whitelisted");``` can be changed to custom errors to save gas.


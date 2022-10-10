    1.  Use '++i'; instead of 'i++' to save gas (found line 199 of ./contracts/BlurExchange.sol).
    2.  Using uint smaller than 32 bytes incurs overhead. Each operation involving a uint8 costs an extra 22-28 gas (found line 199 in ./contracts/BlurExchange.sol, "for (uint8 i = 0; i < length; i++)" ).
    3.  Don't compare boolean expressions to boolean literals. Use '!revokedApproval[from]' instead of 'require(revokedApproval[from] == false' in the require (found line 77 of ./contract/ExecutionDelegate.sol).
    4.  Do not use '+=' ('totalFee += fee;', found line 478 in  ./contract/BlurExchange.sol). Instead use "totalFee = totalFee + fee;" in order to save some gas.
    5.  Require() string longer than 32 bytes cost extra gas. For exemple, line 482 of ./contract/BlurExchange.sol, the require error string ("Total amount of fees are more than the price") is 44 bytes long when "Total amount of fees > price" is 28 bytes long, and therefore cheaper in gas and just as clear.
    6.  Avoid default assignment of i in loops. 'uint8 i' instead of 'uint8 i = 0'. Saves 3 gas per instance. (found line 199 of ./contract/BlurExchange.sol).
    7.  Increment 'i' at the end of the loop in 'unchecked' so save gas. 'unchecked { ++i; }' at end of loop instead of 'for (uint8 i = 0; i < length; i++)'. (found line 199 of ./contract/BlurExchange.sol).
    8.  Use block.chainid to set the chain id instead of passing an address argument to the initializer function.
    9.  Array.length should not be looked up in every loop of a for loop. Should be assigned once before the loop. (ex: orders.length in line 199 of contracts/BlurExchange.sol).
    10. Use assembly to write storage value. Line 238 in setOracle() of ./contract/BlurExchange.sol, instead of "oracle = _oracle;", use  assembly {sstore(oracle.slot, _oracle)} to save gas.
    11. Use custom errors rather than revert() / require() string to save gas. (i.e. revert in _transferTo() function in ./contract/BlurExchange.sol).
## In the file contracts/BlurExchange.sol: 
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

[!]USE A MORE RECENT VERSION OF SOLIDITY.

Line 199: LOOP VARIABLES SHOULD GET CACHED
INCREMENTS CAN BE UNCHECKED:In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

AN ARRAY'S LENGTH SHOULD BE CACHED TO SAVE GAS IN FOR-LOOPS:orders.length

`++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT'S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)

STORAGE VARIABLES LOADED TWOCE SHOULD GET CACHED
In function `_canMatchOrders`:
`policyManager` should be cached to save gas

Line 476: LOOP VARIABLES SHOULD GET CACHED
INCREMENTS CAN BE UNCHECKED:In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
AN ARRAY'S LENGTH SHOULD BE CACHED TO SAVE GAS IN FOR-LOOPS: fees.length
`++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT'S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)

STORAGE VARIABLES LOADED TWOCE SHOULD GET CACHED
In function `_transferTo`:
`weth` should be cached to save gas

STORAGE VARIABLES LOADED TWOCE SHOULD GET CACHED
In function `_executeTokenTransfer`:
`executionDelegate` should be cached to save gas

Line 36:        require(isOpen == 1, "Closed");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 134:        require(sell.order.side == Side.Sell);
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 139:        require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 140:        require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 142:        require(_validateSignatures(sell, sellHash), "Sell failed authorization");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 143:        require(_validateSignatures(buy, buyHash), "Buy failed authorization");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 183:        require(msg.sender == order.trader);
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 219:        require(address(_executionDelegate) != address(0), "Address cannot be zero");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 228:        require(address(_policyManager) != address(0), "Address cannot be zero");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 237:        require(_oracle != address(0), "Address cannot be zero");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 318:            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 407:        require(v == 27 || v == 28, "Invalid v parameter");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 424:            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 428:            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 431:        require(canMatch, "Orders cannot be matched");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 452:            require(msg.value == price);
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 482:        require(totalFee <= price, "Total amount of fees are more than the price");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 482:        require(totalFee <= price, "Total amount of fees are more than the price");
REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

Line 513:            revert("Invalid payment token");
REVERT() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 534:        require(_exists(collection), "Collection does not exist");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 208:        nonces[msg.sender] += 1;
`<X> += <Y>` COSTS MORE GAS THAN `<X> = <X> + <Y>` FOR VARIABLES

Line 71:    mapping(bytes32 => bool) public cancelledOrFilled;
USING `BOOL`S FOR STORAGE INCURS OVERHEAD: use uint256(1) uint256(2) instead.

Line 199:        for (uint8 i = 0; i < orders.length; i++) {
USAGE OF `UINTS`/`INTS` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD: When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Line 347:        uint8 v,
USAGE OF `UINTS`/`INTS` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD: When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Line 383:        uint8 v; bytes32 r; bytes32 s;
USAGE OF `UINTS`/`INTS` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD: When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Line 385:            (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
USAGE OF `UINTS`/`INTS` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD: When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Line 388:            (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
USAGE OF `UINTS`/`INTS` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD: When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Line 403:        uint8 v,
USAGE OF `UINTS`/`INTS` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD: When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Line 476:        for (uint8 i = 0; i < fees.length; i++) {
USAGE OF `UINTS`/`INTS` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD: When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Line 221:        emit NewExecutionDelegate(executionDelegate);
AVOID EMITTING A STORAGE VARIABLE WHEN A MEMORY VALUE IS AVAILABLE

Line 230:        emit NewPolicyManager(policyManager);
AVOID EMITTING A STORAGE VARIABLE WHEN A MEMORY VALUE IS AVAILABLE

Line 239:        emit NewOracle(oracle);
AVOID EMITTING A STORAGE VARIABLE WHEN A MEMORY VALUE IS AVAILABLE

Line 247:        emit NewBlockRange(blockRange);
AVOID EMITTING A STORAGE VARIABLE WHEN A MEMORY VALUE IS AVAILABLE

## In the file contracts/PolicyManager.sol: 
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

[!]USE A MORE RECENT VERSION OF SOLIDITY.

STORAGE VARIABLES LOADED TWOCE SHOULD GET CACHED
In function `removePolicy`:
`_whitelistedPolicies` should be cached to save gas

Line 26:        require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 37:        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

## In the file contracts/ExecutionDelegate.sol: 
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

[!]USE A MORE RECENT VERSION OF SOLIDITY.

Line 22:        require(contracts[msg.sender], "Contract is not approved to make transfers");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 22:        require(contracts[msg.sender], "Contract is not approved to make transfers");
REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

Line 77:        require(revokedApproval[from] == false, "User has revoked approval");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 92:        require(revokedApproval[from] == false, "User has revoked approval");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 108:        require(revokedApproval[from] == false, "User has revoked approval");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 124:        require(revokedApproval[from] == false, "User has revoked approval");
REQUIRE() USED! USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE()/ASSERT() STRINGS TO SAVE GAS.

Line 18:    mapping(address => bool) public contracts;
USING `BOOL`S FOR STORAGE INCURS OVERHEAD: use uint256(1) uint256(2) instead.

Line 19:    mapping(address => bool) public revokedApproval;
USING `BOOL`S FOR STORAGE INCURS OVERHEAD: use uint256(1) uint256(2) instead.


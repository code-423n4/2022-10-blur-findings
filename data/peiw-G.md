- `++i` costs less gas than `i++`, especially when in FOR loops.
	- `++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled. `i++` increments `i` and returns the initial value of `i`. Which means: `uint i = 1; i++; // == 1 but i == 2` But `++i` returns the actual incremented value: `uint i = 1; ++i; // == 2 and i == 2` too, so no need for a temporary variable In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476


- Using unchecked blocks to save gas - increments in for loop can be unchecked (SAVE 30-40 GAS PER LOOP ITERATION).
	- The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476


- `<ARRAY>.length` should not be looked up in every loop of a for-loop.
	- The overheads PER LOOP, excluding the first loop: 1). storage arrays incur a Gwarmaccess (100 gas); 2). memory arrays use MLOAD (3 gas); 3). calldata arrays use CALLDATALOAD (3 gas). Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476


- Usage of `UINTS/INTS` smaller than 32 bytes incurs overhead.
	- When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476


- It costs more gas to initialize NON-CONSTANT/NON-IMMUTABLE variables to zero than to let the default of zero be applied.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77	
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475


- `ABI.ENCODE()` is less efficient than `ABI.ENCODEPACKED()`.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L45
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L61
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L91
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L107
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L132
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L147


- Using `PRIVATE` rather than `PUBLIC` for `CONSTANTS`, saves gas.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L59
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20-L33


- Duplicated REVERT()/REQUIRE() checks should be refactored to a modifier or function.
	- Instances:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124


- Use custom errors rather than REVERT()/REQUIRE() strings to save gas.
	- Mulitple places.


- `x += y` cost more gas than `x = x + y` for state variables.
	- Instance:
		- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479

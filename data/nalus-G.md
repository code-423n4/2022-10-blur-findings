1.	[Gas] AN ARRAY’S LENGTH SHOULD BE CACHED TO SAVE GAS IN FOR-LOOPS
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.


2.	[Gas] INCREMENTS CAN BE UNCHECKED
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.


3.	[Gas] ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 (SAME FOR --I VS I-- OR I -= 1)
Pre-increments and pre-decrements are cheaper.
For a uint256 i variable, the following is true with the Optimizer enabled at 10k:
Increment:
i += 1 is the most expensive form
i++ costs 6 gas less than i += 1
++i costs 5 gas less than i++ (11 gas less than i += 1)

4.	[Gas] IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) should be replaced with for (uint256 i; i < numIterations; ++i) 

5.	[Gas] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()

6.	[Gas] USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS

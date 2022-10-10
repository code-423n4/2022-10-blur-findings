 1. Change NewOracle event name or add `require(address(oracle) != address(_oracle))`.
        - Since the event of setOracle is called 'NewOracle', we either need a require to check if the oracle is indeed new, or change the event's name.

    2. Inconsistent variable names in initializer (i.e. 
       `uint256 chainId,
        address _weth,` )

    3. Always state the units of fee (i.e. feePercentage, feePartsPerThounsand).

    4. Unused import
        - To imporve readability and avoir confusion, consider removing the following unused import:
            - In the BlurExchange contract, the IBlurExchange interface.
            - In the ExecutionDelegate contract, the IExecutionDelegate interface.
            - In the PolicyManager contract, the IPolicyManager interface.
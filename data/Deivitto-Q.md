# QA
# Low
## `ecrcover` not checking for 0 value
### Impact
The ecrecover function is used in `_recover()` to recover the address from the signature. The built-in EVM precompile ecrecover is susceptible to signature malleability which could lead to replay attacks (references: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).
### Github Permalink
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L408
### Recommended Mitigation steps
Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.

## transfer as reentrancy mitigation
### Summary
Fixed gas cost are not good reentrancy mitigations as the cost may change by the time.
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L128-L175

### Mitigation
Avoid using transfer fixed cost as a reentrancy mitigation as the gas cost may change.


## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L113
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L116
### Mitigation
Check zero address before assigning or using it

## block.timestamp used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L283
        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L258-L271
        ((order.trader != address(0)) && (cancelledOrFilled[orderHash] == false) && _canSettleOrder(order.listingTime,order.expirationTime))

### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision

## Front run initializer
### Summary
The initialize function that initializes important contract state can be called by anyone.
### Details
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L95-L102
### Mitigation
Use the constructor to initialize non-proxied contracts.

For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.

## Use of `asserts()`
### Impact

From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
You have reachable asserts in the following locations (which should be replaced by `require` / are mistakenly left from development phase):

### Details
The Solidity `assert()` function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

A bug exists in the contract that allows it to enter an invalid state;
The assert statement is used incorrectly, e.g. to validate inputs.

### References
SWC ID: 110

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol#L22
        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));

### Recommended Mitigation Steps
Substitute `asserts` with `require`/`revert`.

## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions. Not events/Error are being emitted if that fails, so functions would be called even of not being working as expect as for [example `_addCollateral` what would interact with the ether.]

### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L25-L30
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L36-L41
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L496-L515

### Mitigation
Check values and `revert`/`emit` events if needed

## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details 
Contracts has partial or full lack of comments
### Github Permalinks 
- Natspec `@param` and `@return` value

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ReentrancyGuarded.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/matchingPolicies/StandardPolicyERC1155.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/matchingPolicies/StandardPolicyERC721.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/MerkleVerifier.sol
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/OrderStructs.sol

 ### mitigation
 - Add @param descriptors
 - Add @return descriptors
 - Add Natspec comments. 
 - Add inline comments. 
 - Add comments for what the contract does


# Informational
## Comparison with a a boolean
### Summary
There are a number of instances where a boolean variable/function is checked.
### Details
- This check can be further simplified from `variable == false` to `!variable`.

### Github Permalink

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L77
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L92
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L108
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L124
        require(revokedApproval[from] == false, "User has revoked approval");

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L267
            (cancelledOrFilled[orderHash] == false) &&

### Mitigation
Simplify boolean comparisons in order to improve readability and save gas



## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
- Should use any type of index value
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L85
    event OrderCancelled(bytes32 hash);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L86
    event NonceIncremented(address trader, uint256 newNonce);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L88
    event NewExecutionDelegate(IExecutionDelegate executionDelegate);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L89
    event NewPolicyManager(IPolicyManager policyManager);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L90
    event NewOracle(address oracle);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L91
    event NewBlockRange(uint256 blockRange);

- Should emit some value
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L40
    event Opened();

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L41
    event Closed();

### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.

## Naming convention of constants
### Summary
Constant naming convention is all upper case. 
### Details
Some constants are not using proper style.
Constant should be in `UPPER_CASE_WITH_UNDERSCORES` as per Solidity Style Guide. 
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L57
    string public constant name = "Blur Exchange";

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L58
    string public constant version = "1.0";
### Mitigation
Rename the constant to uppercase style: `CONSTANTS_WITH_UNDERSCORES`. 


## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks 

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol#L9

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol#L10

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol#L11

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol#L18

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol#L19

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/ERC1967Proxy.sol#L22

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/ExecutionDelegate.sol#L104

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L24

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/lib/EIP712.sol#L27

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L30

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L124

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L145

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L178

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L205

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L283

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L318

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L387

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L388

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L424

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L425

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L428

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L429

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L437

### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 




## Missing error messages in require statements
### Summary
Require/revert statements should include error messages in order to help at monitoring the system.
### Github Permalinks
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L134
        require(sell.order.side == Side.Sell);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L183
        require(msg.sender == order.trader);

https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/BlurExchange.sol#L452
            require(msg.value == price);

### Mitigation
Add error messages 

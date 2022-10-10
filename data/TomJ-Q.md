## Table of Contents
### Low Risk Issues
- Unhandled Return Values of transferFrom
- Missing Zero Address Check 
- Require should be used instead of Assert
- Avoid Using ecrecover
- Use of block.timestamp

### Non-critical Issues
- Naming Convention for Constants
- Event is Missing Indexed Fields
- Require Statements without Descriptive Revert Strings

&ensp;
## Low Risk Issues
### Unhandled Return Values of transferFrom

#### Issue
There are function in which ERC20 token tranferFrom is not checking the return values.
Since some of the ERC20 tokens does not revert on failure but return false instead, 
it is important to check the return values or otherwise these tokens is still counted as a correct transfer
even thought it didn't actually perform the transfer.

Similar issue reported in public audit:
https://consensys.net/diligence/audits/2020/09/aave-protocol-v2/#unhandled-return-values-of-transfer-and-transferfrom

#### PoC
```solidity
BlurExchange.sol:_transferTo():
511:            executionDelegate.transferERC20(weth, from, to, amount);
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L511
```solidity
ExecutionDelegate.sol:transferERC20():
125:        return IERC20(token).transferFrom(from, to, amount);
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125

#### Mitigation
I recommend to add a require() statement that checks the return values or use OpenZeppelin's SafeERC20 wrapper functions.

&ensp;
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally.

#### PoC
Total of 2 instances found.

1. BlurExchange.sol:constructor():  `weth` address 
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113

2. BlurExchange.sol:constructor():  `oracle` address 
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L116

#### Mitigation
Add 0-address check for above addresses.

&ensp;
### Require should be used instead of Assert

#### Issue
Solidity documents mention that properly functioning code should never reach a failing assert statement
and if this happens there is a bug in the contract which should be fixed.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require

#### PoC
Total of 1 instance found.
```
./ERC1967Proxy.sol:22:        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```

#### Mitigation
Replace assert by require.

&ensp;
### Avoid Using ecrecover

#### Issue
It is best practice to avoide using ecrecover since the ecrecover EVM opcode allows malleable signatures
and thus is vulnerable to reply attacks. Since the contract uses nonce, replay attack seems not possible here
but it is still recommended to use recover instead of ecrecover.
Also ecrecover returns an empty address when the signature is invalid. 
So it is also best practice to add 0-address check.

Reference: https://docs.soliditylang.org/en/v0.8.15/units-and-global-variables.html?highlight=ecrecover#mathematical-and-cryptographic-functions

#### PoC
Total of 1 instance found.
```
./BlurExchange.sol:408:        return ecrecover(digest, v, r, s);
```

#### Mitigation
Use the recover function from OpenZeppelin‚Äôs ECDSA library.
Add zero address check. 

&ensp;
### Use of block.timestamp

#### Issue
When smart contract utilizes the `block.timestamp` function when performing critical logic
such as time-triggered data or generating random numbers, it may cause vulnerability since
minors can manipulate this block.timestamps freely. 
For functions requiring time-triggered data, assess whether or not the scale of the time-dependent event can vary by 15 seconds and maintain integrity.
If so, it may be safe to use a block.timestamp for that function. 
Reference: https://halborn.com/what-is-timestamp-dependence/

#### PoC
```
./BlurExchange.sol:283:        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
```

&ensp;
## Non-critical Issues
### Naming Convention for Constants

#### Issue
Constants should be named with all capital letters with underscores separating words.

Solidity documentation:
https://docs.soliditylang.org/en/v0.8.15/style-guide.html#constants

#### PoC
Below 2 constant variable should follow constants naming convention best practice.
```
./BlurExchange.sol:57:    string public constant name = "Blur Exchange";
./BlurExchange.sol:58:    string public constant version = "1.0";
```

&ensp;
### Event is Missing Indexed Fields

#### Issue
Each event should have 3 indexed fields if there are 3 or more fields.
Add up to 3 indexed fields when possible.

#### PoC
Total of 6 instances found.
```solidity
./BlurExchange.sol:85:    event OrderCancelled(bytes32 hash);
./BlurExchange.sol:86:    event NonceIncremented(address trader, uint256 newNonce);
./BlurExchange.sol:88:    event NewExecutionDelegate(IExecutionDelegate executionDelegate);
./BlurExchange.sol:89:    event NewPolicyManager(IPolicyManager policyManager);
./BlurExchange.sol:90:    event NewOracle(address oracle);
./BlurExchange.sol:91:    event NewBlockRange(uint256 blockRange);
```

&ensp;
### Require Statements without Descriptive Revert Strings

#### Issue
It is best practice to include descriptive revert strings for require statement for readability and auditing.

#### PoC
```
./BlurExchange.sol:134:        require(sell.order.side == Side.Sell);
./BlurExchange.sol:183:        require(msg.sender == order.trader);
./BlurExchange.sol:452:            require(msg.value == price);
```

#### Mitigation
Add descriptive revert strings to easier understand what the code is trying to do.

&ensp;
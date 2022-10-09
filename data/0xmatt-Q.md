# QA/No/Low Report
### Low Issues
| |Issue|Instances|
|-|:-|:-:|
| [L-01] | Missing zero-address checks | 12 |
| [L-02] | Contract approval has no address checks | 1 |
| [L-03] | Contracts with payable functions need recovery functions to rescue stuck tokens | 1 |
| [L-04] | State variables update after external calls | 2 |
| [L-05] | Signature protection bypass for msg.sender | 1 |

17 Instances of 5 distinct Low issues were identified.

### L-01 Missing zero-address checks
Zero-address checks are a common best practice for input validation of critical address parameters, particularly in constructors and setters. Accidental use of zero-addresses may result in unexpected exceptions, failures or require the redeployment of contracts.

Only affected files, functions and affected parameters are listed for brevity. Links to affected code are provided.

*12 Instances of this issue were found:*

In contracts/BlurExchange.sol:

1. [initialize(_weth, _oracle)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L118)
2. [\_executeFundsTransfer(seller, buyer)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L444-L460) (note a zero-value paymentToken address is used for ETH payments)
3. [\_transferFees(from)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L469-L487) (note a zero-value paymentToken address is used for ETH payments)
4. [\_transferTo(from, to)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L496-L515) (note a zero-value paymentToken address is used for ETH payments)
5. [\_executeTokenTransfer(from, to)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L525-L542)

In contracts/ExecutionDelegate.sol:
6. [approveContract(\_contract)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36-L39)
7. [denyContract(\_contract)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45-L48)
8. [transferERC721Unsafe(collection, from, to)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L73-L79)
9. [transferERC721(collection, from, to)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L88-L94)
10. [transferERC1155(collection, from, to)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L104-L110)
11. [transferERC20(token, from, to)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L119-L126)

In contracts/PolicyManager.sol:
12. [addPolicy(policy)](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L25-L30)

An additional instance of this issue was identified in [removePolicy()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36-L41), although blocking the addition of a zero address in addPolicy() should also eliminate the potential removal of a zero address (as there wouldn't be one to remove). As such this finding is not listed here.

### L-02 Contract approval has no address checks

The [approveContract()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L32-L39) function in ExecutionDelegate.sol is used to approve new contracts for transfer functions. The contract code is shown below:

```solidity
function approveContract(address _contract) onlyOwner external {
	contracts[_contract] = true;
	emit ApproveContract(_contract);
}
```

The contract does not verify the address for approval:

1. Is non-zero (see L-01)
2. Is a real address (ie it exists)
3. Is a contract

As such it is possible that an EOA or wrong address could be approved for initiating transfers. Once approved, addresses can call contract transfer functions.

Update the approveContract() function to check that the contract is non-zero, exists and is a contract.

### L-03 Contracts with payable functions should have recovery functions to rescue stuck tokens
The BlurExchange.sol contract has one payable function, [execute()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L123-L175). The contract lacks basic functions to rescue funds stuck in the contract for any reason. Because of this, any ETH funds that become stuck in the contract cannot be returned on removed.

As the contract is ownable, rescue methods could be used to return funds caused by users making mistakes. The code below is just an example of the type of functions the project may want to add.

```solidity
function rescueERC20(address to, address paymentToken, uint256 amount ) external onlyOwner {
	IERC20(paymentToken).safeTransfer(to, amount);
}

/// @dev used for rescuing exchange fees paid to the contract in ETH
function rescueETH(address to, uint256 amount) external payable onlyOwner {
	(bool sent, ) = to.call{value: amount}('');
	require(sent, 'Rescue Failed.');
}
```

Depending on how users interact it might also be beneficial to add recovery functions for ERC721 and ERC1155 tokens sent to the contract.
### L-04 State variables update after external calls
Although a re-entrancy guard modifier is in place, BlurExchange.sol's execute() function marks orders as filled after external calls with various callbacks (e.g. to different tokens). This goes against the standard [Checks Effects Interactions](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html) pattern favoured in solidity development. Normally contracts would update all state variable before external calls take place. The success or failure of those calls is then checked, and if successful, little more than an emit takes place. On failure, the contract reverts.

```solidity
	contracts/BlurExchange.sol:
		_executeFundsTransfer(
			sell.order.trader,
			buy.order.trader,
			sell.order.paymentToken,
			sell.order.fees,
			price
		);
		_executeTokenTransfer(
			sell.order.collection,
			sell.order.trader,
			buy.order.trader,
			tokenId,
			amount,
			assetType
		);

		/* Mark orders as filled. */
		cancelledOrFilled[sellHash] = true;
		cancelledOrFilled[buyHash] = true;
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L147-L165

In BlurExchange.sol we see that external calls are made via \_executeFundsTransfer() and \_executeTokenTransfer, before the two instances of setting order value state as filled. In most programming languages this is fine, but in solidity it breaks the pattern. If for any reason the re-entrancy guard fails, someone could abuse this function to repeatedly invoke execute(), possibly re-executing an order.

Ideally this code should be put above the two external calls, and then the success or failure of those calls should be checked for edge cases priort to continuing or reverting.
### L-05 Signature protection bypass for msg.sender
The [\_validateSignatures()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L286-L332) function has an interesting if statement at the top of the function:

```solidity
if (order.order.trader == msg.sender) {
	return true;
}
```

This if statement has an interesting effect on buy and sell sides, effectively removing signature validation checks for one or more sides of an order. \_validateSignatures() is called twice via the execute() function.

There are 3 potential parties that would invoke execute():

1. The buyer,
2. The seller,
3. A third-party (such as a third-party marketplace)

Other, potentially more serious issues aside, this has some interesting effects. Several fields contained within the Order struct are not individually verified:

1. address trader
2. uint256 amount
3. uint256 listingTime
4. uint256 expirationTime;
5. Fee[] fees;
6. uint256 salt;
7. bytes extraParams;

The buyer, seller, or a third party could freely modify the above fields for either buyer or seller side of the order. This could be abused to manipulate order elements. For example, a partially complete order that fails due to passing the expirationTime window could be resubmitted with modified values if validation were disabled. A malicious third-party contract acting on behalf of a seller could modify fee values and addresses to siphon off payment. Although this wouldn't be the platform's fault, such an edge case would permit this activity.

It's not clear from the supplied information why this condition is used. If this is absolutely needed, consideration should be given to independently validating the supplied trader, listingTime, expirationTime and fees elements.

### Non-Critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N-01] | Unused variables | 1 |
| [N-02] | Missing code coverage | 4 |
| [N-03] | Use immutables instead of constants for expressions | 5 |
| [N-04] | Event fields may need indexing. | 6 |
| [N-05] | Only use constants for literal values | 5 |
| [N-06] | Very long lines may wrap or be missed | 2 |
| [N-07] | Unchecked EnumerableSet return values | 2 |

25 Instances of 7 distinct non-critical issues were identified.

#### N-01 Unused variables
The merklePath variable isn't used and generates a compiler warning. While this isn't a vulnerability, unnecessary compiler warnings are worth avoiding where feasible. The unused variable is declared on Line 388 of BlurExchange.sol:

```solidity
(bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388

To eliminate the warning delete the variable declaration, e.g:
```solidity
(, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

*1 Instance of this issue was found.*

#### N-02 Missing code coverage
The following functions in the following code have no test coverage:

1. No coverage of [contracts/lib/MerkleVerifier/\_verifyProof()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17-L26).
2. No coverage of [contracts/PolicyManager.sol/removePolicy()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L32-L41)
3. No coverage of [contracts/PolicyManager.sol/viewCountWhitelistedPolicies()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L51-L56)
4. No coverage of [contracts/PolicyManager.sol/viewWhitelistedPolicies()](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L58-L82)

While this is not a vulnerability in and of itself, code coverage gaps can make it harder to verify the actions code takes. This may lead to incorrect assumptions about code, or make existing vulnerabilities more difficult to spot. Blur should implement appropriate test coverage to validate these functions' operation and security aspects.

*4 Instances of this issue were found.*

#### N-03 Use immutables instead of constants for expressions
Constants should be used for literal values written to the contract code, while immutables are meant to be used for expressions. The compiler should fix mistakes, although it's better for code to be consistently correct.

*5 Instances of this issue were found:*

```solidity
    contracts/lib/EIP712.sol:
         20┆ bytes32 constant public FEE_TYPEHASH = keccak256(
         21┆     "Fee(uint16 rate,address recipient)"
         22┆ );
          ⋮┆----------------------------------------
         23┆ bytes32 constant public ORDER_TYPEHASH = keccak256(
         24┆     "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
         25┆ );
          ⋮┆----------------------------------------
         26┆ bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
         27┆     "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
         28┆ );
          ⋮┆----------------------------------------
         29┆ bytes32 constant public ROOT_TYPEHASH = keccak256(
         30┆     "Root(bytes32 root)"
         31┆ );
          ⋮┆----------------------------------------
         33┆ bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
         34┆     "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
         35┆ );
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20-22
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23-25
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26-28
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29-31
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L33-35

#### N-04 - Event fields may need indexing.
Events are accessible to off-chain tools. Although indexing events costs gas, this makes events easier to filter. The formula for calculating event cost is `375 + 375 * numberOfIndexedParameters + numberOfUnindexedBits`. Not all event parameters need to be indexed, but indexing event parameters such as role address or key value changes will make it easier to filter when investigating issues or security incidents. 

*There are 6 instances of this issue.*

The following events and parameters should be reviewed from an indexing perspective:

```solidity
   contracts/BlurExchange.sol:
		event OrderCancelled(bytes32 hash); // Consider
		event NonceIncremented(address trader, uint256 newNonce); // Consider
		event NewExecutionDelegate(IExecutionDelegate executionDelegate); // Recommended
		event NewPolicyManager(IPolicyManager policyManager); // Recommended
		event NewOracle(address oracle); // Recommended
		event NewBlockRange(uint256 blockRange); // Consider
```
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L85-91

#### N-05 - Only use constants for literal values
Constants should only be used for literal values written to the contract code, while immutables are meant to be used for expressions. Although the compiler should spot this mistake it's better for code to be consistently correct. Replacing these values with pre-calculated Keccak256 values will also reduce gas.

*There are 5 instances of this issue.*

```solidity
   contracts/lib/EIP712.sol:
		/* Order typehash for EIP 712 compatibility. */
		bytes32 constant public FEE_TYPEHASH = keccak256(
			"Fee(uint16 rate,address recipient)"
		);
		bytes32 constant public ORDER_TYPEHASH = keccak256(
			"Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
		);
		
		bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
			"OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
		);
		
		bytes32 constant public ROOT_TYPEHASH = keccak256(
			"Root(bytes32 root)"
		);
		
		bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
			"EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
		);
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L19-L35

#### N-06 - Very long lines may wrap or be missed
Long lines may wrap or be hidden by scrollbars in some editors or viewers. Content may be missed when hidden. If editors rewrap text incorrectly, errors could be introduced. The most commonly used tools in smart contract development tend not to use an 80 column limit. On a 1920x1080 display, VS Code wraps the terminal at 203 characters, while Github uses a scrollbar for lines longer than 164 characters. As Github is used at least for the C4 competition it may be beneficial to wrap content at 164 characters.

*There are 2 instances of this issue.*

```solidity
	contracts/lib/EIP712.sol:
		bytes32 constant public ORDER_TYPEHASH = keccak256(
			"Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
		);
		
		bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
			"OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
		);
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23-L28

#### N-07 - Unchecked EnumerableSet return values
OpenZeppelin's EnumerableSet library provides an easy way to maintain arrays. When adding or removing entries in a set, [EnumerableSet's methods](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol#L59-L115) return a boolean indicating success or failure. It's generally considered best practice to follow the vendor's recommended approach. The PolicyManager.sol contract implements [it's own functions to add and remove policies](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L21-L41), presumably to support the onlyOwner modifier. However the approach taken doesn't check the OZ function return values. There is a require to see if the policy is inside the set but this isn't the same as checking the return value. The difference is subtle but present.

```solidity
	contract/PolicyManager.sol:
		function addPolicy(address policy) external override onlyOwner {
			require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
			_whitelistedPolicies.add(policy);
			emit PolicyWhitelisted(policy);
		}
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L21-L30

The use of require() allows us to inline the check. This would be a more 'correct' way to deal with policy management via OZ EnumerableSet for both addPolicy and removePolicy instances:

```solidity
	contract/PolicyManager.sol:
		function addPolicy(address policy) external override onlyOwner {
			require(_whitelistedPolicies.add(policy), "Already whitelisted");
			emit PolicyWhitelisted(policy);
		}
			
		/**
		 * @notice Remove matching policy
		 * @param policy address of policy to remove
		 */		
		function removePolicy(address policy) external override onlyOwner {
			require(_whitelistedPolicies.remove(policy), "Not whitelisted");
			emit PolicyWhitelisted(policy);
		}			
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L21-L41

In-lining the policy change will also save gas as OZ will check the set in either case anyway.
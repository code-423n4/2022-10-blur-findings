
### Loop optimizations

Locations:
[1](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
[2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
[3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
[4](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)

Several loops in the codebase can be optimized slightly for better gas characteristics ( > 100 gas savings per loop, depending on the looped calculation).

* Replace repeated length computations with a cached reference
* Replace `i++` in the iterator with an unchecked block and a prefix iterator

An example is provided below:

```
uint256 proofLength = proof.length;
for (uint256 i = 0; i < proofLength;) {
    bytes32 proofElement = proof[i];
    computedHash = _hashPair(computedHash, proofElement);
    
    unchecked {
        ++i;
    }
}
```

#### Dead Code

Locations:
[1](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17)
[2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L73)

The code indicated above is neither called by any contracts in the system nor are they trivial view/getter functions. They could be removed to save gas during deployment.

#### State check on `revokedApproval` setters

Locations:
[1](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L53)
[2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L61)

A pair of functions provided by `ExecutionDelegate` allow a user to approve or revoke the contract permissions for token transfers.

The functions lack a check to see if the bool they are setting isn't already set. This means the user could waste gas (~51370) on erroneous subsequent calls to `revokeApproval()` and `grantApproval()` if the corresponding state (`revokedApproval`) has already been set.

A simple `if` can prevent wasted gas at the cost of ~300 gas for normal state changes. If the `if` isn't taken (state is already set) then the savings is 24628 gas.

An example modification is provided below.

```
function revokeApproval() external {
    if (!revokedApproval[msg.sender]) { 
        revokedApproval[msg.sender] = true;
        emit RevokeApproval(msg.sender);
    }
}
```

Test suite passed normally, ensuring the logic was not tampered with.

#### State change for `isOpen` can be changed to `1|2` instead of `0|1` for gas savings when re-opening after a closure

Because of the high cost of setting a non-0 value in storage values, gas can be saved if the `isOpen` value and equality test in the `whenOpen()` modifier use `1` and `2` instead of `0` and `1`.

The following output from Foundry shows the gas values of setting the exchange to open, then to closed, and then back to open. The first sample is the default code, and the second is with the modifications.

```
    ├─ [1684] ERC1967Proxy::open() 
    │   ├─ [1292] BlurExchange::open() [delegatecall]
    │   │   ├─ emit Opened()
    │   │   └─ ← ()
    │   └─ ← ()
    ├─ [1352] ERC1967Proxy::close() 
    │   ├─ [1038] BlurExchange::close() [delegatecall]
    │   │   ├─ emit Closed()
    │   │   └─ ← ()
    │   └─ ← ()
    ├─ [21584] ERC1967Proxy::open() 
    │   ├─ [21192] BlurExchange::open() [delegatecall] <--- large gas cost
    │   │   ├─ emit Opened()
    │   │   └─ ← ()
    │   └─ ← ()
    └─ ← ()
```

```
    ├─ [1684] ERC1967Proxy::open() 
    │   ├─ [1292] BlurExchange::open() [delegatecall]
    │   │   ├─ emit Opened()
    │   │   └─ ← ()
    │   └─ ← ()
    ├─ [1686] ERC1967Proxy::close() 
    │   ├─ [1294] BlurExchange::close() [delegatecall]
    │   │   ├─ emit Closed()
    │   │   └─ ← ()
    │   └─ ← ()
    ├─ [1684] ERC1967Proxy::open() 
    │   ├─ [1292] BlurExchange::open() [delegatecall]
    │   │   ├─ emit Opened()
    │   │   └─ ← ()
    │   └─ ← ()
    └─ ← ()
```

```
modifier whenOpen() {
    require(isOpen == 2, "Closed");
    _;
}

event Opened();
event Closed();

function open() external onlyOwner {
    isOpen = 2;
    emit Opened();
}

function close() external onlyOwner {
    isOpen = 1;
    emit Closed();
}

[...]

function initialize(uint chainId, address _weth, IExecutionDelegate _executionDelegate, IPolicyManager _policyManager, address _oracle, uint _blockRange) 
    public initializer {
    __Ownable_init();
        isOpen = 2;  // <---- Updated to new open value

        DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({name: name, version: version, chainId: chainId, verifyingContract: address(this)}));

        weth = _weth;
        executionDelegate = _executionDelegate;
        policyManager = _policyManager;
        oracle = _oracle;
        blockRange = _blockRange;
}
```

Test suite was run to ensure logic was unaffected.

#### `+=` notation is slightly more expensive than simple `+` for state values.

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L207)

Using `stateValue = stateValue + 1` is slightly cheaper. In this case, the `incrementNonce()` function saves 113 gas on each use. Below, the function is called twice in order to demonstrate the differences between the code as-is and the modified code.

```
    ├─ [24399] ERC1967Proxy::incrementNonce() 
    │   ├─ [24007] BlurExchange::incrementNonce() [delegatecall]
    │   │   ├─ emit NonceIncremented(trader: Deploy: [0x5b73C5498c1E3b4dbA84de0F1833c4a029d90519], newNonce: 1)
    │   │   └─ ← ()
    │   └─ ← ()
    ├─ [2499] ERC1967Proxy::incrementNonce() 
    │   ├─ [2107] BlurExchange::incrementNonce() [delegatecall]
    │   │   ├─ emit NonceIncremented(trader: Deploy: [0x5b73C5498c1E3b4dbA84de0F1833c4a029d90519], newNonce: 2)
    │   │   └─ ← ()
    │   └─ ← ()
    └─ ← ()
```

```
    ├─ [24286] ERC1967Proxy::incrementNonce() 
    │   ├─ [23894] BlurExchange::incrementNonce() [delegatecall]
    │   │   ├─ emit NonceIncremented(trader: Deploy: [0x5b73C5498c1E3b4dbA84de0F1833c4a029d90519], newNonce: 1)
    │   │   └─ ← ()
    │   └─ ← ()
    ├─ [2386] ERC1967Proxy::incrementNonce() 
    │   ├─ [1994] BlurExchange::incrementNonce() [delegatecall]
    │   │   ├─ emit NonceIncremented(trader: Deploy: [0x5b73C5498c1E3b4dbA84de0F1833c4a029d90519], newNonce: 2)
    │   │   └─ ← ()
    │   └─ ← ()
    └─ ← ()
```

```
function incrementNonce() external {
    nonces[msg.sender] = nonces[msg.sender] + 1;
    emit NonceIncremented(msg.sender, nonces[msg.sender]);
}
```

#### Using `payable` attribute on functions which apply access control modifiers can save small amounts of gas.

Locations:
[1](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L215)
[2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L224)
[3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L233)
[4](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L242)
[5](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L36)
[6](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L45)
[7](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L73)
[8](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L88)
[9](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L104)
[10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L119)
[11](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L25)
[12](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L36)


Applying the `payable` attribute to functions saves gas because the EVM skips a check for `value` in the call to the function, with the intent to revert if value is non-zero. For functions which normal users cannot interact with, the risk of sending ETH by mistake is low, and the calls save gas. Savings are slightly higher when the caller is an EOA (~2 gas).

Below is an example of gas savings between the as-is code and code changed to add `payable`. Note that applicable interfaces require modification as well.

```
    ├─ [2260] ERC1967Proxy::setExecutionDelegate(0x0000000000000000000000000000000000000001) 
    │   ├─ [1865] BlurExchange::setExecutionDelegate(0x0000000000000000000000000000000000000001) [delegatecall]
    │   │   ├─ emit NewExecutionDelegate(executionDelegate: 0x0000000000000000000000000000000000000001)
    │   │   └─ ← ()
    │   └─ ← ()
    └─ ← ()
```

```
    ├─ [2236] ERC1967Proxy::setExecutionDelegate(0x0000000000000000000000000000000000000001) 
    │   ├─ [1841] BlurExchange::setExecutionDelegate(0x0000000000000000000000000000000000000001) [delegatecall]
    │   │   ├─ emit NewExecutionDelegate(executionDelegate: 0x0000000000000000000000000000000000000001)
    │   │   └─ ← ()
    │   └─ ← ()
    └─ ← ()
```

#### Replace repeated storage reads with cached value.

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L71)

Reading values from storage repeatedly is gas-inefficient. Create a local scoped value for cheaper stack reads.

❗ Note that `view` functions will only save gas if they are called by contracts during execution.

As an example, below is an example of lower gas after making the following modification.

```
uint256 policyLength = _whitelistedPolicies.length();
if (length > policyLength  - cursor) {
    length = policyLength - cursor;
}
```

```
    ├─ [2478] PolicyManager::viewWhitelistedPolicies(0, 3) [staticcall]
    │   └─ ← [0xdaE97900D4B184c5D2012dcdB658c008966466DD, 0x238213078DbD09f2D15F4c14c02300FA1b2A81BB], 2
    └─ ← ()
```


```
    ├─ [2406] PolicyManager::viewWhitelistedPolicies(0, 2) [staticcall]
    │   └─ ← [0xdaE97900D4B184c5D2012dcdB658c008966466DD, 0x238213078DbD09f2D15F4c14c02300FA1b2A81BB], 2
    └─ ← ()
```

#### Use `if` with custom errors rather than `require` + revert strings

Locations:
[1](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26)
[2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)
[3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
[4](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
[5](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
[6](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
[7](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)
[8](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L128) <- Many instances
[9](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
[10](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407)


❗Some examples should be factored out into a modifier if possible, since they implement the same check repeatedly. This recommendation applies if a modifier isn't desired.

It is cheaper to use an `if` construct combined with `revert` and a custom error than a `require`. Using custom errors often helps debug and UX experience more cheaply than expressive revert strings, and can contain types to help with clarity.

For example, the following code changes the function at location 1 above.

```
error ContractAlreadyWhitelisted(address);

function addPolicy(address policy) external override onlyOwner {
    if (_whitelistedPolicies.contains(policy)) { revert ContractAlreadyWhitelisted(policy); }
    _whitelistedPolicies.add(policy);

    emit PolicyWhitelisted(policy);
}
```

```
    ├─ [815] PolicyManager::addPolicy(StandardPolicyERC721: [0xdaE97900D4B184c5D2012dcdB658c008966466DD]) 
    │   └─ ← "Already whitelisted"
```


```
    ├─ [791] PolicyManager::addPolicy(StandardPolicyERC721: [0xdaE97900D4B184c5D2012dcdB658c008966466DD]) 
    │   └─ ← "ContractAlreadyWhitelisted(0xdaE97900D4B184c5D2012dcdB658c008966466DD)"
```

#### Convert repeated `require` check into a modifier

Locations:
[1](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
[2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
[3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
[4](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)

To save gas on deployments, repeated/common checks should be replaced with a modifier. It also aids readability.

For example, the `require` in these locations should be replaced with an `approved` modifier, preferrably implementing an `if () {revert}` with a custom error, as mentioned in another finding.

#### public `weth` value should be changed to `immutable`

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L63)

This storage value can be removed and converted to a value stored in the bytecode of the contract, as there is no function in the contract that modifies its value.

#### Remove extraneous computation from `OrdersMatched` event emission

[Location](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L167)

Unless there is a compelling business case for recording the maker/taker relationship of a trade, removing the computation for who was the maker and taker would save nearly 500 gas on a core hot path of the protocol.

Consider instead simply recording the seller and buyer addresses. Since the inputs to `execute` already contain the `listingTime` via the `Order` struct, the information can be computed offchain rather than forcing users to pay for the computation.


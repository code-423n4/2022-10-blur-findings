### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01 ]| Insufficient coverage file | |
| [N-02] |Include ``return parameters`` in _NatSpec comments_|All contracts|
| [N-03] | `0` address check | 12 |
| [N-04] | `DOMAIN_TYPEHASH` can change in future | 2 |
| [N-05] | Long lines are not suitable for the `Solidity Style Guide` | 5 |
| [N-06] | `abicoder v2` is enabled by default | 1 |
| [N-07] | Use a more recent version of Solidity | All contracts |
| [N-08] | `Require` revert cause should be known | 4 |
| [N-09] | Use `require` instead of `assert` | 1 |
| [N-10] | For modern and more readable code; update import usages | 3 |
| [N-11] | `WETH` address definition can be use _directly_ | 1 |
| [N-12] | `Empty blocks` should be _removed_ or _Emit_ something | 1 |
| [N-13] | `Function writing` that does not comply with the `Solidity Style Guide` | 1 |
| [N-14] | Importing IERC20 is sufficient for ERC20 transactions | 1 |
| [N-15] | Implement some type of version counter that will be incremented automatically for contract upgrades|  |
| [N-16] | Use underscores for number literals| 1 |
| [N-17] | Not using the named return variables anywhere in the function is confusing| 1 |
| [N-18] | Compliance with NatSpec rules in Constant expressions| 1 |
| [N-19] | NatSpec is Missing| 8 |
| [N-20] | Omissions in Events| 4 |
| [N-21] | `BlurExchange.sol`  missing initial ownership Event|  |
| [N-22] | There is no need to use the `_exists` function|  |
| [N-23] | Constant values such as a call to keccak256(), should used to immutable rather than constant|  |

Total 23 issues


### Low Risk Issues List
| Number |Issues Details|Context
|:--:|:-------|:--:|
|[L-01]| Add to _blacklist function_ | |
|[L-02]| Highest risk must be specified in NatSpec comments and documentation| |
|[L-03]| Use `abi.encode()` to avoid collision| 4 |
|[L-04]| Add to indexed parameter for countable Events| 2 |
|[L-05]| Use `block.number` instead _block.timestamp_| 1 |
|[L-06]|Use `Ownable2StepUpgradeable` instead of ` OwnableUpgradeable ` contract | 1 |
|[L-07]| Unused import | 1 |
|[L-08]| Use ```SafeTransferOwnership``` instead of ```transferOwnership``` function | 1 |
|[L-09]| Owner can renounce Ownership | 1 |

Total 9 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Generate perfect code headers every time |
| [S-02] | Mark visibility of initialize(...) functions as ``external`` |

Total 2 suggestions


### [N-01] Insufficient coverage file

**Description:**
The test coverage rate of the PolicyManager.sol file is 26%. Testing all functions is best practice in terms of security criteria.
```js
-----------------------------|----------|----------|----------|----------|----------------|
File                         |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
-----------------------------|----------|----------|----------|----------|----------------|
 contracts/                  |    90.34 |    78.57 |    87.18 |    89.86 |                |
  BlurExchange.sol           |    99.12 |    86.76 |       96 |    99.05 |            503 |
  ExecutionDelegate.sol      |    88.24 |       60 |    88.89 |    88.89 |          77,78 |
  PolicyManager.sol          |    26.67 |    16.67 |       40 |    26.67 |... 75,77,78,81 |
 contracts/lib/              |    91.67 |       25 |    92.86 |    88.89 |                |
  EIP712.sol                 |      100 |      100 |      100 |      100 |                |
  ERC1967Proxy.sol           |      100 |      100 |      100 |      100 |                |
  MerkleVerifier.sol         |       75 |        0 |       75 |       70 |       22,23,24 |
  OrderStructs.sol           |      100 |      100 |      100 |      100 |                |
  ReentrancyGuarded.sol      |      100 |       50 |      100 |      100 |                |
 contracts/matchingPolicies/ |      100 |      100 |      100 |      100 |                |
  StandardPolicyERC1155.sol  |      100 |      100 |      100 |      100 |                |
  StandardPolicyERC721.sol   |      100 |      100 |      100 |      100 |                |
 contracts/test/             |      100 |      100 |      100 |      100 |                |
  TestBlurExchange.sol       |      100 |      100 |      100 |      100 |                |
-----------------------------|----------|----------|----------|----------|----------------|
All files                    |    90.91 |    76.14 |       90 |    90.12 |                |
-----------------------------|----------|----------|----------|----------|----------------|
```

### [N-02] Include ``return parameters`` in _NatSpec comments_

**Context:**
All contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

If Return parameters are declared, you must prefix them with "/// @return".

Some code analysis programs do analysis by reading NatSpec details, if they can't see the "@return" tag, they do incomplete analysis.

**Recommendation:**
Include return parameters in NatSpec comments

Recommendation Code Style:
 ```js
    /// @notice information about what a function does
    /// @param pageId The id of the page to get the URI for.
    /// @return Returns a page's URI if it has been minted 
    function tokenURI(uint256 pageId) public view virtual override returns (string memory) {
        if (pageId == 0 || pageId > currentId) revert("NOT_MINTED");

        return string.concat(BASE_URI, pageId.toString());
    }
```

### [N-03] `0 address` check

**Context:**
[BlurExchange.sol#L445-L446](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L445-L446)
[BlurExchange.sol#L498-L499](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L498-L499)
[BlurExchange.sol#L471-L472](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L471-L472)
[BlurExchange.sol#L346](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L346)
[BlurExchange.sol#L95](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95)
[ExecutionDelegate.sol#L36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36)
[ExecutionDelegate.sol#L46](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L46)
[EIP712.sol#L16](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L16)
[ERC1967Proxy.sol#L21](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L21) 
[BlurExchange.sol#L116](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L116)
[BlurExchange.sol#L113](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L113)
[BlurExchange.sol#L499](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L499)

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
`if (_treasury == address(0)) revert ADDRESS_ZERO();`


### [N-04] DOMAIN_TYPEHASH can change in future

**Context:**
[EIP712.sol#L33](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L33)
[EIP712.sol#L39](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L39)

**Description:**
DOMAIN_TYPEHASH is assigned to the contract as a constant, the chain id value may change in hardforks, so a possible hardfork will also become invalid after deployment.

**Recommendation:**
```js
 constructor() {
        DOMAIN_SEPARATOR_CHAIN_ID = block.chainid;
        _DOMAIN_SEPARATOR = _calculateDomainSeparator();
    }

    function _calculateDomainSeparator() internal view returns (bytes32 domainSeperator) {
        domainSeperator = keccak256(
            abi.encode(
                keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),
                keccak256(bytes(name)),
                keccak256(bytes("1")),
                block.chainid,
                address(this)
            )
        );
    }

    /// @notice EIP-712 typehash for this contract's domain.
    function DOMAIN_SEPARATOR() public view returns (bytes32 domainSeperator) {
        domainSeperator = block.chainid == DOMAIN_SEPARATOR_CHAIN_ID ? _DOMAIN_SEPARATOR : _calculateDomainSeparator();
    }
```

### [N-05] Long lines are not suitable for the `Solidity Style Guide`

**Context:**
[EIP712.sol#L24](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L24)
[EIP712.sol#L27](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L27)
[BlurExchange.sol#L388](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388)
[BlurExchange.sol#L425](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L425)
[BlurExchange.sol#L429](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L429)

**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]

**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction


### [N-06] `abicoder v2` is enabled by default

**Context:**
[ExecutionDelegate.sol#L3](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L3)

**Description:**
abicoder v2 is considered non-experimental as of Solidity 0.6.0 and it is enabled by default starting with Solidity 0.8.0. Therefore, there is no need to write

[abi-coder-pragma](https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html#abi-coder-pragma)


### [N-07] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
The solidity version 0.8.13 has below two issues:

Vulnerability related to `ABI-encoding`.
ref : https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/

Vulnerability related to `Optimizer Bug Regarding Memory Side Effects of Inline Assembly`
ref : https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/

**Recommendation:**
Old version of Solidity is used `(0.8.13)`, newer version can be used `(0.8.17)`


### [N-08] Require revert cause should be known

*Context:**
[BlurExchange.sol#L134](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134)
[BlurExchange.sol#L183](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183)
[BlurExchange.sol#L452](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452)
[BlurExchange.sol#L513](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L513)

**Description:**
Vulnerability related to  description is not written to require, it must be written so that the user knows why the process is reverted.

**Recommendation:**
Current Code:
```js
require(sell.order.side == Side.Sell);
```
Recommendation Code:
```js
require(sell.order.side == Side.Sell), "Sell has invalid parameters");
```


### [N-09] Use `require` instead of `assert`

**Context:**
[ERC1967Proxy.sol#L22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L22)

**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".


### [N-10] For modern and more readable code; update import usages

**Context:**
[BlurExchange.sol#L5-L16](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L5-L16)
[ExecutionDelegate.sol#L5-L8](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L5-L8)
[ERC1967Proxy.sol#L5_L6](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L5_L6)

**Description:**
Our Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`


### [N-11] `WETH` address definition can be use _directly_

**Context:**
[BlurExchange.sol#L63](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63)

**Description:**
WETH is a wrap Ether contract with a specific address in the Etherum network, giving the option to define it may cause false recognition, it is healthier to define it directly.

Advantages of defining a specific contract directly:
- It saves gas,
- Prevents incorrect argument definition, 
- Prevents execution on a different chain and re-signature issues,

WETH Address : 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

```js
constructor(address _manager, address _weth) payable initializer {
        manager = IManager(_manager);
        WETH = _weth;
    }
```

**Recommendation:**
```js
address private constant WETH = 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2;

    constructor(address _manager) payable initializer {
        manager = IManager(_manager);
}
```


### [N-12] `Empty blocks` should be _removed_ or _Emit_ something

**Context:**
[BlurExchange.sol#L53](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53)

**Description:**
Code contains empty block.

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

_Current code:_
```js
    function _authorizeUpgrade(address _newImpl) internal override onlyOwner {}
```

_Suggested code:_
```js
    function _authorizeUpgrade(address _newImpl) internal override onlyOwner {
        emit _authorizeUpgrade(address _newImpl);
}
```


### [N-13] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last


### [N-14] Importing IERC20 is sufficient for ERC20 transactions

**Context:**
[BlurExchange.sol#L8](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L8)

**Description:**
In order to run the functions of erc20, it is sufficient to import only IERC20.


### [N-15] Implement some type of version counter that will be incremented automatically for contract upgrades

**Description:**
As part of the upgradeability of ```UUPS Proxies```, the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```js
uint256 public authorizeUpgradeCounter;

function _authorizeUpgrade(address _newImpl) internal view override onlyOwner {	
        authorizeUpgradeCounter+=1;
    }
```


### [N-16] Use underscores for number literals

[BlurExchange.sol#L59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)

**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.


### [N-17] Not using the named return variables anywhere in the function is confusing

**Context:**
[BlurExchange.sol#L419](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L419)

**Recommendation:**
Consider changing the variable to be an unnamed one.


### [N-18] Compliance with NatSpec rules in Constant expressions

**Context:**
[BlurExchange.sol#L57-L58](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L58)

**Recommendation:**
Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.


### [N-19] NatSpec is Missing

NatSpec is missing for the following function:

[MerkleVerifier.sol#L49](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L49)
[MerkleVerifier.sol#L45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L45)
[BlurExchange.sol#L242](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242)
[BlurExchange.sol#L233](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233)
[BlurExchange.sol#L224](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224)
[BlurExchange.sol#L215](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215)
[BlurExchange.sol#L47](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47)
[BlurExchange.sol#L43](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43)


### [N-20] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:
Events with no old value;;
[BlurExchange.sol#L239](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L239)
[BlurExchange.sol#L230](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L230)
[BlurExchange.sol#L221](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L221)
[BlurExchange.sol#L209](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L209)


### [N-21] `BlurExchange.sol`  missing initial ownership Event

The BlurExchange.initialize function does not emit an initial OwnershipTransferred event.

Recommended Mitigation Steps
In initialize, ``emit OwnershipTransferred(address(0), owner_)``


### [N-22] There is no need to use the `_exists` function

`_exists` function is used for `_executeTokenTransfer` functions assert collection exist.
if an address contains code, it's not an EOA but a contract account. However, a contract does not have source code available during construction. This means that while the constructor is running, it can make calls to other contracts, but extcodesize for its address returns zero. Using this is inefficient  

```js
function _executeTokenTransfer(
        address collection,
        address from,
        address to,
        uint256 tokenId,
        uint256 amount,
        AssetType assetType
    ) internal {
        /* Assert collection exists. */
        require(_exists(collection), "Collection does not exist");

        /* Call execution delegate. */
        if (assetType == AssetType.ERC721) {
            executionDelegate.transferERC721(collection, from, to, tokenId);
        } else if (assetType == AssetType.ERC1155) {
            executionDelegate.transferERC1155(collection, from, to, tokenId, amount);
        }
    }



    /**
     * @dev Determine if the given address exists
     * @param what address to check
     */

    function _exists(address what)
        internal
        view
        returns (bool)
    {
        uint size;
        assembly {
            size := extcodesize(what)
        }
        return size > 0;
    }
}
```

Recommended Mitigation Steps
Remove to `require` from function;
``require(_exists(collection), "Collection does not exist");``


### [N-23] Constant values such as a call to keccak256(), should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

There are 4 instances of this issue:
```js
contracts/lib/EIP712.sol:

  20:     bytes32 constant public FEE_TYPEHASH = keccak256(...
  23:     bytes32 constant public ORDER_TYPEHASH = keccak256(...
  26:     bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(...
  29:     bytes32 constant public ROOT_TYPEHASH = keccak256(...
```

### [L-01] Add to _blacklist function_

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808
In addition, these platforms even ban accounts that have received ETH on their account with Tornadocash.

Some of these Projects;
 USDC (https://www.circle.com/en/usdc)
 Flashbots (https://www.paradigm.xyz/portfolio/flashbots )
 Aave (https://aave.com/)
 Uniswap
 Balancer
 Infura
 Alchemy 
 Opensea
 dYdX 

Details on the subject;
https://twitter.com/bantg/status/1556712790894706688?s=20&t=HUTDTeLikUr6Dv9JdMF7AA

https://cryptopotato.com/defi-protocol-aave-bans-justin-sun-after-he-randomly-received-0-1-eth-from-tornado-cash/

For this reason, every project in the Ethereum network must have a blacklist function, this is a good method to avoid legal problems in the future, apart from the current need.

Transactions from the project by an account funded by Tonadocash or banned by OFAC can lead to legal problems.Especially American citizens may want to get addresses to the blacklist legally, this is not an obligation

If you think that such legal prohibitions should be made directly by validators, this may not be possible:
https://www.paradigm.xyz/2022/09/base-layer-neutrality

```The ban on Tornado Cash makes little sense, because in the end, no one can prevent people from using other mixer smart contracts, or forking the existing ones. It neither hinders cybercrime, nor privacy.```

Here is the most beautiful and close to the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps add to Blacklist function and modifier.


### [L-02] Highest risk must be specified in NatSpec comments and documentation

**Description:**
Although the UUPS model has many advantages and the proposed proxy model is currently the UUPS model, there are some caveats we should be aware of before applying it to a real-world project.

One of the main attention: Since upgrades are done through the implementation contract with the help of the ```upgradeTo``` method, there is a high risk of new implementations such as excluding the ```upgradeTo``` method, which can permanently kill the smart contract's ability to upgrade. Also, this pattern is somewhat complicated to implement compared to other proxy patterns.

**Recommendation:**
Therefore, this highest risk must be found in NatSpec comments and documents.


### [L-03] Use `abi.encode()` to avoid collision 

**Context:**
[EIP712.sol#L80](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L80)
[EIP712.sol#L117](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L117)
[EIP712.sol#L129](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L129)
[EIP712.sol#L144](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L144)

**Description:**
If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of a into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c"). If you use abi.encodePacked for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, abi.encode should be preferred.

_**When are these used?**_
```abi.encode:```
abi.encode encode its parameters using the ABI specs. The ABI was designed to make calls to contracts.
Parameters are padded to 32 bytes. If you are making calls to a contract you likely have to use abi.encode
If you are dealing with more than one dynamic data type as it prevents collision.

```abi.encodePacked:```
abi.encodePacked encode its parameters using the minimal space required by the type. Encoding an uint8 it will use 1 byte. It is used when you want to save some space, and not calling a contract.Takes all types of data and any amount of input.

**Proof of Concept:**
```js
contract Contract0 {

    // abi.encode
    // (AAA,BBB) keccak = 0xd6da8b03238087e6936e7b951b58424ff2783accb08af423b2b9a71cb02bd18b
    // (AA,ABBB) keccak = 0x54bc5894818c61a0ab427d51905bc936ae11a8111a8b373e53c8a34720957abb
    function collision(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encode(_text, _anotherText));
    }
}

contract Contract1 {

    // abi.encodePacked
    // (AAA,BBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358
    // (AA,ABBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358

    function hard(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encodePacked(_text, _anotherText));
    }
}
```

### [L-04] Add to indexed parameter for countable Events

**Context:**
[BlurExchange.sol#L86](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L86)
[BlurExchange.sol#L90](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L90)

**Recommendation:**
Add to indexed parameter for countable Events.


### [L-05] Use `block.number` instead _block.timestamp_

**Context:**
[BlurExchange.sol#L283](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L283)

**Description:**
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

**Recommendation:**
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking) , It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.


### [L-06] Use `Ownable2StepUpgradeable` instead of ` OwnableUpgradeable ` contract

**Context:**
[BlurExchange.sol#L7](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L7)

**Description:**
```transferOwnership``` function is used to change Ownership from ```OwnableUpgradeable.sol```.

There is another Openzeppelin Ownable contract (Ownable2StepUpgradeable.sol) has  ` transferOwnership` function ,  use it is more secure due to 2-stage ownership transfer.

[Ownable2StepUpgradeable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/access/Ownable2StepUpgradeable.sol)


### [L-07] Unused import

**Context:**
[BlurExchange.sol#L8](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L8)

**Description:**
ERC20.sol import file is not used, if ERC20 functions are to be used, IERC20 must be imported


### [L-08] Use ```safeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**
[ExecutionDelegate.sol#L8](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L8)

**Description:**
```transferOwnership``` function is used to change Ownership from ```Ownable.sol```.

Use a 2 structure transferOwnership which is safer. 
```safeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)


### [L-09] Owner can renounce Ownership

**Context:**
[BlurExchange.sol#L7](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L7)

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The Openzeppelin’s Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

`onlyOwner` functions;
```js
7 results - 3 files

contracts/BlurExchange.sol:
  42  
  43:     function open() external onlyOwner {
  44          isOpen = 1;

  47:     function close() external onlyOwner {
  48          isOpen = 0;

  53:     function _authorizeUpgrade(address) internal override onlyOwner {}
  54  

contracts/ExecutionDelegate.sol:
  51:     function approveContract(address _contract) onlyOwner external {
  52          contracts[_contract] = true;

  63:     function denyContract(address _contract) onlyOwner external {
  64          contracts[_contract] = false;

contracts/PolicyManager.sol:
  25:     function addPolicy(address policy) external override onlyOwner {
  26          require(!_whitelistedPolicies.contains(policy), "Already whitelisted");

  36:     function removePolicy(address policy) external override onlyOwner {
  37          require(_whitelistedPolicies.contains(policy), "Not whitelisted");
```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.


### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers


### [S-02] Mark visibility of initialize(...) functions as ``external``

**Description:**
If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}_init function, not the parent public initialize(...) function.

External instead of public would give more the sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally)

Security point of view, it might be safer so that it cannot be called internally by accident in the child contract 

It might cost a bit less gas to use external over public 


It is possible to override a function from external to public (= "opening it up") ✅
but it is not possible to override a function from public to external (= "narrow it down"). ❌

For above reasons you can change initialize(...) to external


https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750
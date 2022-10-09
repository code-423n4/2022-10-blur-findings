 ## `Withdrawerc721`: using the `transferFrom()` function of an erc721 contract may freeze the user's nft 
 ### Files Found: 
 There are 2 instances of this issue. 
 - File: contracts/ExecutionDelegate.sol - line 78 
 - File: contracts/ExecutionDelegate.sol - line 125 
 
 
 ### Mitigation: 
 
When using the transferFrom function of an ERC721 contract to send an NFT, if the receiving address is a smart contract and does not support ERC721, the NFT can be frozen in the contract. Use the ERC721 contract's safeTransferFrom function to send NFTs. 

 --- 

## Unused/empty `receive()/fallback()` function 
 ### Files Found: 
 There are 1 instances of this issue. 
 - File: contracts/BlurExchange.sol - line 53 
 
 ### Mitigation: 
 Either implement, emit or remove the empty callback 

 --- 

## Findings: State variables only set in the constructor should be declared immutable

### Files Found:

- [https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L63](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L97)
- Line 63 - 66

### Mitigation:

Where a storage variable is assigned only once. These variables should be declared immutable.

---

## Findings: Missing zero-address check in the setter functions and initiliazers

### Files Found:

- File: [https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L25](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L25)

### Mitigation:

Consider adding zero-address checks in the discussed constructors: require(newAddr != address(0));.

---

## Findings: Consider using disableInitializers for UUPS/ Upgradable Contracts

Explanation: `[_disableInitializers()`](https://docs.openzeppelin.com/contracts/4.x/api/proxy#Initializable-_disableInitializers--)  was added in Contracts v4.6.0 as part of the reinitializers support. By putting it in the constructor, this prevents initialization of the **implementation**
 contract itself, as extra protection [to prevent an attacker from initializing it](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680). Recommended by open zepellin.

Source: [https://forum.openzeppelin.com/t/what-does-disableinitializers-function-mean/28730](https://forum.openzeppelin.com/t/what-does-disableinitializers-function-mean/28730)

### Files Found:

- File: [https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/interfaces/IBlurExchange.sol#L14](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/interfaces/IBlurExchange.sol#L14)

### Mitigation:

- Consider using `[_disableInitializers()`](https://docs.openzeppelin.com/contracts/4.x/api/proxy#Initializable-_disableInitializers--)
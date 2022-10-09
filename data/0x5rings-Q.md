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
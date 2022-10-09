Low Risk Findings
---
## No support for trading of Cryptopunks (or any other ERC721 non-compliant NFTs)
#### Impact
Cryptopunks do not adhere to the `ERC721` standard, so it cannot be traded on Blur exchange which requires NFT to be `ERC721` compliant (i.e. have `safeTransferFrom()` implemented). Because Cryptopunks are a huge part of NFT ecosystems, this may put Blur exchange at a disadvantage over other NFT exchanges.

This issue is similar to previous contest issue: https://github.com/code-423n4/2022-02-foundation-findings/issues/74.

PS: similar issues from previous contests were judged as Medium. Submitting as Low just  so the project knows that they do not support certain notable NFTs. I personally disagree that the impact for not supporting non ERC721 compliant NFTs to be medium.

#### Proof of Concept
Here is an example [implementation](https://github.com/code-423n4/2021-12-nftx/blob/main/nftx-protocol-v2/contracts/solidity/NFTXStakingZap.sol#L417-L424) of what it might look like to integrate cryptopunks into the protocol.

#### Recommendations
Consider adding support for cryptopunks.


---
## Risk of signature reuse across forks due to lack of chainID validation
The `BlurExchange.sol` contract computes the `DOMAIN_SEPERATOR` at initialization with `chainId` argument. In the event of a chain fork, the `chainId` cannot be updated and signatures can be replayed across both versions of the chain.
```sol
function _hashDomain(EIP712Domain memory eip712Domain)
        internal
        pure
        returns (bytes32)
    {
        return keccak256(
            abi.encode(
                EIP712DOMAIN_TYPEHASH,
                keccak256(bytes(eip712Domain.name)),
                keccak256(bytes(eip712Domain.version)),
                eip712Domain.chainId,
                eip712Domain.verifyingContract
            )
        );
    }
```
#### Impact
If a fork of Ethereum is made after the contract's initialization, every signature can be used on both forks. If a user signs a maker order on Ethereum mainnet and later, Ethereum is hard-forked and **retains the same chain ID**, an attacker can replay the user's signature to match orders on the forked chain.

#### Recommendations
To prevent signature reuse in the event of forks, either use OZ's implementation or edit the current `EIP712.sol` contract to update chainId when forks occur.
```sol
function _domainSeparatorV4() internal view returns (bytes32) {
	if (address(this) == _CACHED_THIS && block.chainid == _CACHED_CHAIN_ID) {
		return _CACHED_DOMAIN_SEPARATOR;
	} else {
		return _buildDomainSeparator(_TYPE_HASH, _HASHED_NAME, _HASHED_VERSION);
	}
}
```
This will ensure that when `chain.id` changes, it will update the `DOMAIN_SEPERATOR` accordingly, preventing replay attacks.

More details: https://eips.ethereum.org/EIPS/eip-1344#rationale


---
### `ecrecover()` not checked for address(0) signer
```sol
function _recover(
	bytes32 digest,
	uint8 v,
	bytes32 r,
	bytes32 s
) internal pure returns (address) {
	require(v == 27 || v == 28, "Invalid v parameter");
	return ecrecover(digest, v, r, s);
}
```
Bad practice, although I see no way in the protocols current implementation where it could be exploited.
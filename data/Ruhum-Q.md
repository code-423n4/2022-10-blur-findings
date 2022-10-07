# Report

- [Low](#low)
  - [L-01: Exchange doesn't support Cryptopunks](#l-01-exchange-doesnt-support-cryptopunks)
- [Non-Critical](#non-critical)
  - [N-01: abicoder v2 is activated by default](#n-01-abicoder-v2-is-activated-by-default)

# Low

## L-01: Exchange doesn't support Cryptopunks

Cryptopunks don't follow the ERC721 standard and won't be compatible with the exchange. It's the biggest NFT collection out there. Other marketplaces have added custom logic to support them.

Here's an example from the NFTx contest last year: https://github.com/code-423n4/2021-12-nftx/blob/main/nftx-protocol-v2/contracts/solidity/NFTXStakingZap.sol#L417-L424

# Non-Critical

## N-01: abicoder v2 is activated by default

Explicitly selecting `abicoder v2` is unnecessary since Solidity v0.8.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L3

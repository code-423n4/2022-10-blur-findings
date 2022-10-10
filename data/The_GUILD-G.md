# Unconventional Naming Strategy: 

In the MerkleVerifier, there are four functions in total named with an underscore in front of them. This is against the conventional way of naming private, internal, external and public functions. This conventional naming strategy is relevant to help understand the codebase and avoid any form of misconception in differentiating between the functions.

## Context

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L33

## Recommendation

Remove the underscore before the _verifyProof and _computeRoot; both being public functions. An appropriate way to name both private and internal functions is for them to have an underscore in front of them. This model of naming strategy helps identify functions easily and eliminate any form of misunderstanding.
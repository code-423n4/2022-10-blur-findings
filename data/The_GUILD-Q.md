Low level severity Issue

Missing Address Zero Check for Approval and Denial of contracts: This function omits the checks for when address zero inputs are passed into it. While this function is for the motive of approving contracts, it gives approved addresses the right to execute calls. Allowing for address zero/null address to be set to true could expose the contract to potential exploits. 

Context:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L36

Recommendation: There should be an additional check to prevent setting the null address to true. If a malicious person gains ownership access to the marketplace, they have the tendencies of exploiting the marketplace.